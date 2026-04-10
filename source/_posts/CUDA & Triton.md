---
title: 从0到算子工程师（上）：CUDA & Triton算子实践全路径
date: 2026-04-10 08:45:36
tags:
- AI CUDA Trition
categories:
- 算子
---



------

# 从0到算子工程师（上）：CUDA & Triton算子实践全路径

> 因为最近在做信创化改造，涉及到一些适配工作，也就搞起了算子开发的一些工作，这个博客分为两个部分，上篇是依据trition的官方文档实践的基础入门，下篇会涉及到vllm等框架。
>
> 整体内容肯定是用AI润色，稍微有点AI风。
>
> 另外，最后的效果，我在colab上反复测试，发现trition如果不调优的话，在矩阵乘法的效率上是低于pytorch的原生api的，这个初学者需要注意，后面入门后可以继续优化。

------

# 一、整体学习路线

```
阶段1：计算图理解
阶段2：PyTorch自定义算子
阶段3：CUDA基础
阶段4：CUDA实现AI算子
阶段5：Triton算子
```

------

# 二、阶段1：理解Transformer计算图

## 1. 核心目标

这一阶段不是写代码，而是理解：

- 模型是如何由“算子”组成的
- Transformer 的计算本质

------

## 2. Transformer核心算子

核心只有几类：

```
MatMul（最重要）
Softmax
LayerNorm
Add / Mul
Activation（GELU）
```

------

## 3. Attention计算流程

```
QKV projection
↓
QK^T
↓
Scale
↓
Softmax
↓
Attn × V
↓
Linear
```

------

## 4. 实践代码：打印计算图（模块级）

```python
import torch
from transformers import BertModel

model = BertModel.from_pretrained("bert-base-uncased")

for name, module in model.named_modules():
    print(name, type(module))
```

------

## 5. 进阶：算子级计算图（非常关键）

```python
import torch
from torch.fx import symbolic_trace

class MyModel(torch.nn.Module):
    def forward(self, x, y):
        a = torch.matmul(x, y)
        b = torch.softmax(a, dim=-1)
        c = torch.add(b, 1)
        return c

model = MyModel()
traced = symbolic_trace(model)

print(traced.graph)
```

------

## 6. 阶段验收

需要能回答：

- Transformer核心算子是什么？
- Attention计算流程
- 一个Transformer layer有多少次MatMul（答案：8次）

------

# 三、阶段2：PyTorch自定义算子

## 1. 核心目标

理解：

```
forward = 计算
backward = 梯度
autograd = 计算图
```

------

## 2. Add算子实现

```python
import torch

class MyAdd(torch.autograd.Function):
    @staticmethod
    def forward(ctx, a, b):
        return a + b

    @staticmethod
    def backward(ctx, grad_output):
        return grad_output, grad_output


x = torch.tensor(2.0, requires_grad=True)
y = torch.tensor(3.0, requires_grad=True)

z = MyAdd.apply(x, y)
z.backward()

print(x.grad, y.grad)
```

------

## 3. 与官方算子对比（工程级验证）

```python
x = torch.tensor(2.0, requires_grad=True)
y = torch.tensor(3.0, requires_grad=True)

# 官方
z1 = x + y
z1.backward()
grad1 = (x.grad.item(), y.grad.item())

x.grad.zero_()
y.grad.zero_()

# 自定义
z2 = MyAdd.apply(x, y)
z2.backward()
grad2 = (x.grad.item(), y.grad.item())

print("official:", grad1)
print("custom :", grad2)
```

------

## 4. ReLU算子实现

```python
class MyReLU(torch.autograd.Function):

    @staticmethod
    def forward(ctx, x):
        ctx.save_for_backward(x)
        return torch.clamp(x, min=0)

    @staticmethod
    def backward(ctx, grad_output):
        x, = ctx.saved_tensors
        grad = grad_output.clone()
        grad[x < 0] = 0
        return grad


x = torch.tensor([-1.0, 2.0, 3.0], requires_grad=True)

y = MyReLU.apply(x)
y.sum().backward()

print(x.grad)
```

------

## 5. LayerNorm（forward实现）

```python
class MyLayerNorm(torch.autograd.Function):

    @staticmethod
    def forward(ctx, x, eps=1e-5):
        mean = x.mean()
        var = x.var(unbiased=False)

        y = (x - mean) / torch.sqrt(var + eps)

        ctx.save_for_backward(x, mean, var)
        ctx.eps = eps

        return y
```

------

## 6. 阶段验收

必须做到：

```
forward正确
backward正确
与PyTorch结果一致
```

------

# 四、阶段3：CUDA基础

## 1. 核心概念

```
thread
block
grid
shared memory
warp
```

------

## 2. Vector Add Kernel

```cpp
__global__ void vector_add(float *a, float *b, float *c, int n)
{
    int i = blockIdx.x * blockDim.x + threadIdx.x;
    if(i < n)
        c[i] = a[i] + b[i];
}
```

------

## 3. Kernel调用

```cpp
vector_add<<<256, 256>>>(a, b, c, n);
```

------

## 4. 阶段验收

完成：

```
vector add
vector multiply
reduction sum
```

并验证 CPU / GPU 一致。

------

# 五、阶段4：CUDA实现AI核心算子

这一阶段是**算子工程核心能力分水岭**。

------

## 1. MatMul

核心公式：

```
C[i,j] = sum_k A[i,k] * B[k,j]
```

（典型优化：block tiling + shared memory）

------

## 2. Softmax

实现流程：

```
1 max
2 减max
3 exp
4 sum
5 normalize
```

------

## 3. LayerNorm

genui{"math_block_widget_always_prefetch_v2": {"content": "y = \frac{x - \mu}{\sqrt{\sigma^2 + \epsilon}} \cdot \gamma + \beta"}}

------

## 4. 阶段验收

```
CUDA MatMul
CUDA Softmax
CUDA LayerNorm
```

验证：

```python
torch.allclose()
```

------

# 六、阶段5：Triton算子

## 1. 为什么学Triton？

现代高性能算子：

- FlashAttention
- xFormers

基本都基于 Triton。

------

## 2. Triton Vector Add

```python
import torch
import triton
import triton.language as tl

@triton.jit
def vector_add_kernel(x_ptr, y_ptr, out_ptr, n_elements, BLOCK_SIZE: tl.constexpr):
    pid = tl.program_id(axis=0)

    block_start = pid * BLOCK_SIZE
    offsets = block_start + tl.arange(0, BLOCK_SIZE)

    mask = offsets < n_elements

    x = tl.load(x_ptr + offsets, mask=mask)
    y = tl.load(y_ptr + offsets, mask=mask)

    output = x + y

    tl.store(out_ptr + offsets, output, mask=mask)
```

------

## 3. Triton MatMul（核心结构）

```python
@triton.jit
def matmul_kernel(
    a_ptr, b_ptr, c_ptr,
    M, N, K,
    stride_am, stride_ak,
    stride_bk, stride_bn,
    stride_cm, stride_cn,
    BLOCK_M: tl.constexpr,
    BLOCK_N: tl.constexpr,
    BLOCK_K: tl.constexpr,
):
    pid = tl.program_id(axis=0)

    grid_n = tl.cdiv(N, BLOCK_N)
    pid_m = pid // grid_n
    pid_n = pid % grid_n

    offs_m = pid_m * BLOCK_M + tl.arange(0, BLOCK_M)
    offs_n = pid_n * BLOCK_N + tl.arange(0, BLOCK_N)
    offs_k = tl.arange(0, BLOCK_K)

    acc = tl.zeros((BLOCK_M, BLOCK_N), dtype=tl.float32)

    for k_start in range(0, K, BLOCK_K):
        a_ptrs = a_ptr + (offs_m[:, None] * stride_am +
                         (k_start + offs_k)[None, :] * stride_ak)

        b_ptrs = b_ptr + ((k_start + offs_k)[:, None] * stride_bk +
                         offs_n[None, :] * stride_bn)

        a = tl.load(a_ptrs)
        b = tl.load(b_ptrs)

        acc += tl.dot(a, b)

    c_ptrs = c_ptr + (offs_m[:, None] * stride_cm +
                     offs_n[None, :] * stride_cn)

    tl.store(c_ptrs, acc)
```

------

## 4. 阶段验收

```
Triton vector add
Triton matmul
```

并对比：

- PyTorch正确性
- 性能差异

这里就只贴出矩阵乘法的实验结果了，因为这个是明显有性能差距的。

```
M=256, K=256, N=256 | Triton=0.0520 ms, Torch=0.0496 ms
M=512, K=512, N=512 | Triton=0.1748 ms, Torch=0.1335 ms
M=1024, K=1024, N=1024 | Triton=1.0823 ms, Torch=0.6990 ms
```

