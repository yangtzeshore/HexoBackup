---
title: 企业级大模型运维（LLMOps）架构深度实践：从模型服务到 Agent 评估的全链路平台设计
date: 2026-03-09 10:22:16
tags:
- AI Infra LLMOps MLOps
categories:
- LLMOps
---



------

# 企业级大模型运维（LLMOps）架构深度实践：从模型服务到 Agent 评估的全链路平台设计

写在前面：这篇文章是目前我这一年实践MLOps/LLMOps的沉淀。虽然目前主要精力还是在微调和agent开发，但是开发的应用越多，越发现平台治理的问题，所以在年底的时候，花了很大的功夫将整个平台做了一次升级和改造。因为涉及的方面比较多，有些就简单带过，有经验的同学应该能明白大体的架构和逻辑。另外，也让AI帮忙润色修改了很多，有些AI风，请见谅。

在企业落地大模型应用的过程中，很多团队最初的关注点往往集中在 **模型能力本身**：选择哪个模型、如何微调、如何构建 RAG。然而随着系统进入生产环境，一个更复杂的问题逐渐浮现：

> **如何像运维微服务一样运维大模型？**

当系统规模扩大后，企业通常会遇到以下问题：

- 模型数量迅速增长（LLM、Embedding、Rerank、小模型）
- GPU 成本持续上升
- 推理延迟不稳定
- Agent 系统难以调试
- RAG 幻觉难以评估
- 线上模型无法安全发布

这些问题的本质，都是 **LLMOps（Large Language Model Operations）** 问题。

本文将从企业 AI 平台架构视角，系统介绍一套 **生产级 LLMOps 全链路架构**，覆盖：

1. AI Gateway 与多模型统一服务
2. vLLM + Kubernetes 的高性能推理架构
3. 全栈监控体系（Infra / Model / Business）
4. 模型生命周期管理（MLflow + 微调训练）
5. Agent 与 RAG 的可观测性与评估
6. 生产环境成本优化与延迟控制

目标是构建一套 **稳定、可扩展、可观测的企业级 AI 平台**。

------

# 一、企业级 LLM 平台总体架构

在企业实践中，大模型平台通常会演化成如下架构：

```
                ┌─────────────────────┐
                │      Client / App    │
                │ Chatbot / Agent / BI │
                └──────────┬──────────┘
                           │
                    ┌───────────────┐
                    │   AI Gateway   │
                    │ Auth / Route   │
                    └───────┬───────┘
                            │
       ┌────────────────────┼────────────────────┐
       │                    │                    │
 ┌─────────────┐     ┌─────────────┐      ┌─────────────┐
 │   LLM       │     │ Embedding   │      │  SmallModel │
 │   vLLM      │     │  Service    │      │ ONNX/Triton │
 └─────────────┘     └─────────────┘      └─────────────┘

 Application Layer
 ├ RAG Service
 ├ Agent Service
 └ Workflow Engine

 Observability
 ├ Prometheus + Grafana
 ├ ELK
 └ Langfuse

 Lifecycle
 ├ MLflow
 └ Training Platform (LLaMA-Factory)
```

这套架构可以拆分为五个核心模块：

| 模块            | 职责             |
| --------------- | ---------------- |
| AI Gateway      | 模型统一入口     |
| Inference Layer | 高性能推理       |
| Observability   | 全链路监控       |
| Lifecycle       | 模型管理         |
| Evaluation      | RAG / Agent 评估 |

下面逐一展开。

------

# 二、AI Gateway：统一模型服务入口

在企业内部，模型种类通常非常多，例如：

- 通用大模型（Qwen / LLaMA）
- Embedding 模型
- Rerank 模型
- OCR / CV 小模型
- 语音模型

如果每个模型都独立提供 API，很快会出现：

- 接口不统一
- 鉴权混乱
- 限流困难
- 发布难以管理

因此 **AI Gateway 是企业 LLM 平台的核心组件**。

------

## 1 统一 API 协议

工业界普遍采用 **OpenAI API 协议** 作为统一接口。

例如：

```
POST /v1/chat/completions
POST /v1/embeddings
```

无论底层是：

- vLLM
- Triton
- ONNX Runtime

对外接口保持一致。

------

## 2 多模型路由

Gateway 可以根据 model 字段进行路由。

示例：

```yaml
routes:
  - model: qwen72b
    backend: vllm-qwen72b

  - model: embedding-bge
    backend: embedding-service
```

这样应用无需关心底层部署方式。

------

## 3 流控与配额

在企业环境中必须实现 **多租户限流**。

常见策略：

- 请求 QPS
- Token QPS
- 并发限制

例如：

```
Team A : 500 RPM
Team B : 100 RPM
```

限流可以使用：

- Envoy
- Kong
- APISIX

------

## 4 灰度发布支持

AI Gateway 也是 **模型灰度发布的关键组件**。

例如：

```
model v1 → 90%
model v2 → 10%
```

配置示例：

```yaml
traffic_split:
  qwen72b_v1: 0.9
  qwen72b_v2: 0.1
```

------

# 三、高性能推理架构：vLLM + Kubernetes

在企业生产环境中，LLM 推理性能是最核心的问题之一。

如果直接使用：

```
Transformers + FastAPI
```

GPU 利用率通常只有 **20%**。

目前工业界主流方案是：

> **vLLM + Kubernetes**

------

# vLLM 核心优化

vLLM 的关键技术是：

**PagedAttention**

其核心思想是：

- KV Cache 按页管理
- 动态 batch 合并
- 避免显存碎片

效果：

| 方案            | GPU利用率 |
| --------------- | --------- |
| HF Transformers | 20-30%    |
| TGI             | 40-50%    |
| vLLM            | 70-90%    |

------

# Kubernetes 推理部署

典型部署架构：

```
             ┌──────────────┐
             │  AI Gateway  │
             └───────┬──────┘
                     │
               ┌──────────┐
               │   HPA     │
               └─────┬────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
   ┌────────┐  ┌────────┐  ┌────────┐
   │ vLLM-1 │  │ vLLM-2 │  │ vLLM-3 │
   └────────┘  └────────┘  └────────┘
```

------

## GPU 调度

Kubernetes GPU 调度依赖：

```
nvidia-device-plugin
```

Pod 示例：

```yaml
resources:
  limits:
    nvidia.com/gpu: 1
```

------

## 自动扩容

HPA 可以基于以下指标扩容：

- QPS
- GPU utilization
- Token throughput

例如：

```
2 → 8 pods
```

------

## GPU 资源共享

企业还会部署大量小模型：

- OCR
- CV
- ONNX

解决方案：

### MIG

A100 支持 GPU 分区：

```
A100
 ├ 10GB
 ├ 10GB
 └ 20GB
```

### GPU Sharing

也可以使用：

```
Volcano Scheduler
```

进行 GPU slice。

------

# 四、全栈监控体系

LLM 系统的监控复杂度远高于传统服务。

建议采用 **三层监控体系**：

```
Infra → Model → Business
```

------

# 1 基础设施层

监控 GPU 与节点。

组件：

- NVIDIA GPU Exporter
- Prometheus
- Grafana

关键指标：

| 指标            | 说明      |
| --------------- | --------- |
| GPU Utilization | GPU使用率 |
| Memory Used     | 显存占用  |
| Power Draw      | 功耗      |
| Temperature     | 温度      |

------

# 2 推理服务层

vLLM 会暴露 Prometheus 指标：

```
/metrics
```

关键指标：

| 指标             | 意义       |
| ---------------- | ---------- |
| request_latency  | 请求延迟   |
| tokens_generated | 生成token  |
| batch_size       | 动态 batch |

示例：

```
vllm_request_latency_seconds
```

------

# 3 业务层监控

业务日志通常通过 **ELK** 收集。

组件：

- Elasticsearch
- Logstash
- Kibana

日志示例：

```json
{
 "model":"qwen72b",
 "latency":2.3,
 "tokens":430,
 "cost":0.02
}
```

这些数据可以用于：

- 成本分析
- 延迟排查
- 用户行为分析

------

# 五、模型生命周期管理

模型上线不仅是部署，还包括：

- 实验
- 版本
- 回滚

企业通常使用：

**MLflow**

------

# MLflow 功能

MLflow 管理：

- 实验记录
- 参数
- 模型版本

示例：

```
experiment: rag_embedding_exp

metrics:
 recall@5
 ndcg
```

------

# 微调训练

微调通常使用：

**LLaMA-Factory**

支持：

- LoRA
- QLoRA
- DPO
- SFT

训练完成后：

```
model → MLflow registry
```

------

# 六、LLM Observability：Langfuse

传统监控无法解决 LLM 系统的核心问题：

**链路复杂。**

例如一个 Agent：

```
User Query
 → RAG
 → Tool Call
 → LLM
 → SQL
 → LLM
```

如果没有完整 Trace，几乎无法调试。

------

# Langfuse Trace

Langfuse 可以记录：

```
Trace
 ├ Retrieval
 ├ Rerank
 ├ Tool Call
 └ LLM Generate
```

每一步都会记录：

- latency
- tokens
- cost

------

# 七、RAG 系统评估

RAG 最大的问题是：

**幻觉（Hallucination）**

评估需要两个层面。

------

## 1 检索质量

指标：

```
Recall@k
MRR
nDCG
```

例如：

```
top5 是否包含正确文档
```

------

## 2 生成质量

可以使用：

**LLM-as-a-Judge**

示例：

```
Given context and answer
judge if hallucination exists
```

------

# 八、Agent 系统可观测性

Agent 系统的复杂度远高于 RAG。

典型流程：

```
Planner
 → Tool
 → Memory
 → LLM
```

Langfuse 可以记录完整链路：

```
Trace
 ├ Planning
 ├ Tool search
 ├ SQL query
 └ Final answer
```

可以分析：

| 步骤 | 耗时  |
| ---- | ----- |
| RAG  | 200ms |
| SQL  | 300ms |
| LLM  | 2s    |

这样可以快速定位瓶颈。

------

# 九、生产对话机器人成本优化

企业部署 LLM 后，最大的问题往往是：

**成本。**

------

## 1 小模型优先

策略：

```
简单问题 → 7B
复杂问题 → 72B
```

------

## 2 Prompt 压缩

减少：

```
history tokens
```

例如：

- 对话摘要
- Sliding window

------

## 3 语义缓存

使用：

```
Redis + embedding cache
```

对于常见问题：

```
直接返回
```

------

## 4 Streaming

开启：

```
token streaming
```

可以显著降低用户感知延迟。

------

# 十、完整企业 LLMOps 架构总结

最终完整架构如下：

```
                     Client
                       │
                 ┌──────────┐
                 │ AI Gateway│
                 └─────┬─────┘
                       │
        ┌──────────────┼───────────────┐
        │              │               │
     vLLM           Embedding        ONNX
     Service         Service        Models

Application Layer
 ├ RAG
 ├ Agent
 └ Chatbot

Observability
 ├ Prometheus
 ├ Grafana
 ├ ELK
 └ Langfuse

Lifecycle
 ├ MLflow
 └ LLaMA-Factory
```

------

# 结语

企业级 LLMOps 的目标是：

> **让大模型像微服务一样稳定运行。**

核心原则：

1. **统一入口（AI Gateway）**
2. **高效推理（vLLM）**
3. **多层监控体系**
4. **模型生命周期管理**
5. **RAG / Agent 可观测性**

当这些能力完善后，企业才能真正把：

- RAG
- Agent
- AI Copilot
- 数据分析助手

稳定运行在生产环境。

------

