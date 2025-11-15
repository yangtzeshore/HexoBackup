---
title: 利用Colab玩转Onnx——包括可以同时编写和运行Python和C++代码
date: 2025-11-15 11:15:22
tags:
- Onnx Colab CXX
categories:
- AI
---





公司没有提供带显卡的笔记本（贼抠门），也不敢瞎用公司的服务器显卡，双十一犹豫了半天也没买新电脑（经济下行啊）。但是最近有点迷AI部署，所以想到了用Colab，试试能不能搭建Onnx环境，没想到可以。



Colab 本质上是一个运行在 Google 服务器上的 Ubuntu Linux 虚拟机，它预装了 C++ 编译器（如 `g++`）。

我们将通过以下步骤实现：

1. **挂载 Google Drive**：用于访问和保存模型、代码。
2. **安装 C++ ONNX Runtime 库**：下载预编译的 C++ 库文件。
3. **准备一个 ONNX 模型**：我们将用 Python (Colab 的强项) 创建一个简单的模型并保存到 Drive。
4. **编写 C++ 推理代码**：使用 Colab 的 "cell magic" `%%writefile` 将 C++ 代码写入文件。
5. **编译 C++ 代码**：使用 `g++` 编译，并链接到 ONNX Runtime 库。
6. **运行 C++ 程序**：执行编译后的文件。

下面是一个完整的 Colab 笔记本单元格，可以**按顺序复制并运行**它们。

------



### 完整的 Colab 运行步骤



在 Colab 中打开一个新的 Notebook，然后依次执行以下代码单元格。



#### 步骤 1：挂载 Google Drive



这会弹出一个授权窗口，允许 Colab 访问 Google Drive。

Python

```
from google.colab import drive
drive.mount('/content/drive')

# 为了方便，我们创建一个工作目录
# 可以改成任何路径
WORK_DIR = '/content/drive/MyDrive/Colab_CPP_ONNX'
!mkdir -p "{WORK_DIR}"
```



#### 步骤 2：下载并解压 C++ ONNX Runtime



我们将从 GitHub 下载官方的预编译版本。

Python

```
# 我们将把它安装在 Colab 临时的 /content/ 目录下
%cd /content/

# 下载 ONNX Runtime (以 1.16.3 版本为例，你可以去官网查看最新版)
# 注意：这是一个后台命令，前面的 ! 很重要
!wget https://github.com/microsoft/onnxruntime/releases/download/v1.16.3/onnxruntime-linux-x64-1.16.3.tgz

# 解压缩
!tar -xzf onnxruntime-linux-x64-1.16.3.tgz

# 为方便后续编译，我们定义一个路径变量
ORT_DIR = '/content/onnxruntime-linux-x64-1.16.3'
print(f"ONNX Runtime C++ 库已解压到: {ORT_DIR}")

# 安装onnx包
%pip install onnx
```



#### 步骤 3：使用 Python 创建一个简单的 ONNX 模型



我们将创建一个非常简单的模型：`y = 2 * x`。我们用 Python/Torch 来创建它，并保存到你的 Google Drive。

Python

```python
import torch
import torch.nn as nn

# 1. 定义一个简单的 PyTorch 模型
class SimpleModel(nn.Module):
    def forward(self, x):
        return x * 2

# 2. 实例化模型并准备导出
model = SimpleModel()
model.eval()
dummy_input = torch.randn(1, 3) # 假设输入是一个 [1, 3] 的张量

# 3. 定义模型保存路径
MODEL_PATH = f"{WORK_DIR}/simple_model.onnx"

# 4. 导出为 ONNX
torch.onnx.export(
    model,
    dummy_input,
    MODEL_PATH,
    input_names=['input_x'],   # C++ 代码中需要用到
    output_names=['output_y'], # C++ 代码中需要用到
    opset_version=11
)

print(f"模型已保存到: {MODEL_PATH}")
```



#### 步骤 4：编写 C++ 推理代码



我们使用 `%%writefile` 将这个单元格的内容保存为 `infer.cpp` 文件。

C++

```
%%writefile infer.cpp
#include <iostream>
#include <vector>
#include <string>
#include <onnxruntime_cxx_api.h> // ONNX Runtime C++ API

int main() {
    // 1. 初始化 ONNX Runtime 环境
    // Ort::Env 是线程安全的，建议全局创建一个
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "test_env");
    
    // 2. 设置会话选项 (Session Options)
    Ort::SessionOptions session_options;
    session_options.SetIntraOpNumThreads(1); // 设置线程数
    
    // 3. 定义模型路径 (这里使用了硬编码的路径)
    // 注意：路径必须和步骤 3 中保存的路径一致
    const char* model_path = "/content/drive/MyDrive/Colab_CPP_ONNX/simple_model.onnx";

    // 4. 创建会话 (Session)
    Ort::Session session(env, model_path, session_options);
    
    // 5. 获取输入/输出节点信息
    // 我们需要知道输入/输出的名称（在步骤3中定义过）
    Ort::AllocatorWithDefaultOptions allocator;
    const char* input_name = "input_x";
    const char* output_name = "output_y";

    // 6. 准备输入数据
    std::vector<float> input_data = {1.0f, 2.0f, 3.0f};
    std::vector<int64_t> input_shape = {1, 3}; // 形状 [1, 3]
    
    // 7. 创建输入张量 (Tensor)
    auto memory_info = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
    Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
        memory_info, 
        input_data.data(), 
        input_data.size(), 
        input_shape.data(), 
        input_shape.size()
    );

    // 8. 运行推理
    std::vector<Ort::Value> output_tensors = session.Run(
        Ort::RunOptions{nullptr}, 
        &input_name,   // 输入节点名称
        &input_tensor, // 输入张量
        1,             // 输入数量
        &output_name,  // 输出节点名称
        1              // 输出数量
    );

    // 9. 获取输出结果
    float* output_data = output_tensors[0].GetTensorMutableData<float>();
    size_t output_size = output_tensors[0].GetTensorTypeAndShapeInfo().GetElementCount();

    // 10. 打印结果
    std::cout << "--- C++ ONNX Runtime 推理结果 ---" << std::endl;
    std::cout << "输入数据: [ ";
    for(float val : input_data) { std::cout << val << " "; }
    std::cout << "]" << std::endl;

    std::cout << "输出数据 (y = 2 * x): [ ";
    for(int i = 0; i < output_size; ++i) {
        std::cout << output_data[i] << " ";
    }
    std::cout << "]" << std::endl;

    return 0;
}
```



#### 步骤 5：编译 C++ 代码



这是关键一步。我们将使用 `g++` 编译器，并指定 ONNX Runtime 的 `include`（头文件）和 `lib`（库文件）路径。

Python

```
# ORT_DIR 变量是我们在步骤 2 中定义的
!g++ infer.cpp \
    -o run_infer \
    -I "{ORT_DIR}/include" \
    -L "{ORT_DIR}/lib" \
    -lonnxruntime \
    -std=c++17

print("编译完成！生成了可执行文件 'run_infer'")
```



#### 步骤 6：运行 C++ 程序！



编译成功后，当前目录 (`/content/`) 下会有一个名为 `run_infer` 的可执行文件。

Python

```
# 动态链接库 (.so 文件) 需要被系统找到
# 我们将 ONNX 库的路径添加到 LD_LIBRARY_PATH 环境变量中
# "&&" 确保在同一 shell 会话中执行
!export LD_LIBRARY_PATH="{ORT_DIR}/lib" && ./run_infer
```



### 预期输出



如果你正确执行了所有步骤，步骤 6 的输出应该是：

```
--- C++ ONNX Runtime 推理结果 ---
输入数据: [ 1 2 3 ]
输出数据 (y = 2 * x): [ 2 4 6 ]
```

------



### 总结



这个流程展示了如何将 Colab 的 Python 环境（用于模型准备）和其底层的 Linux C++ 环境（用于编译和运行）结合起来。后续可以基于这个 `infer.cpp` 模板，将其修改为你需要的、更复杂的 ONNX 模型。后续应该还是不买笔记本（还是好希望有个本地跑的N卡）。
