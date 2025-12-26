---
title: 构建企业级提示词管理平台：从“硬编码”转向 PromptOps
date: 2025-12-26 08:31:44
tags:
- AI Infra platform Prompt 
categories:
- platform
---



------

# 构建企业级提示词管理平台：从“硬编码”转向 PromptOps

在大型语言模型（LLM）应用的开发过程中，开发者往往会面临“提示词意面（Prompt Spaghetti）”的困境：提示词散落在代码各处、版本管理混乱、缺乏性能监控以及难以进行 A/B 测试。

为了解决这些痛点，开发了一套基于 **FastAPI**  的提示词管理平台（Prompt Management System）。本文将深度解析该系统的核心设计与实现。

完整的代码在：https://github.com/yangtzeshore/prompt_platform/tree/main



## 一、 系统核心架构

该平台不仅仅是一个存储提示词的数据库，它集成了渲染引擎、指标监控和实验框架。其核心技术栈包括：

- **后端框架**：FastAPI 2

- **数据库**：SQLAlchemy (SQLite)

- **模板引擎**：Jinja2

- **分词器**：Tiktoken (GPT-4)

- **容器化**：Docker 3

  

------

## 二、 关键特性解析

### 1. 模块化组件：Snippet 模式

在 `engine.py` 中，引入了 **Snippet（片段）** 的概念。通过 `render_template_with_snippets` 函数，可以将通用的提示词片段（如：特定的 JSON 输出格式、系统人设）预先存入数据库。

Python

```
# engine.py 核心逻辑
def render_template_with_snippets(db: Session, template_str: str, variables: dict):
    snippets = db.query(Snippet).all()
    snippet_dict = {f"snippet_{s.name}": s.content for s in snippets}
    combined_vars = {**variables, **snippet_dict}
    template = Template(template_str)
    rendered_text = template.render(**combined_vars)
    # ... 计算 Token 和 耗时
```

这种设计实现了提示词的**解耦与复用**。当你需要更新全局的“法律免责声明”片段时，只需修改一个 Snippet，所有关联的提示词都会自动同步。

### 2. 灰度发布与 A/B 测试

为了安全地迭代提示词，系统在 `main.py` 中实现了 `/v1/prompts/render-ab` 接口。它根据预设的权重（`weight_a`），在两个版本（`version_a` vs `version_b`）之间进行流量分发。

这为评估新提示词对业务指标的影响提供了客观的数据支持，是实现 **PromptOps** 闭环的关键环节。

### 3. LLM-as-a-Judge：自动化评价系统

提示词好不好，让更强的 LLM 来评价。在 `evaluator.py` 中，集成了通义千问（Qwen）作为“裁判”。

Python

```
def llm_judge(prompt_content: str, output: str, criteria: str):
    # 构建裁判提示词，要求返回 JSON 格式
    judge_prompt = f"..."
    response = client.chat.completions.create(
        model="qwen-plus",
        messages=[{"role": "user", "content": judge_prompt}],
        response_format={"type": "json_object"}
    )
    return response.choices[0].message.content
```

通过这种方式，可以快速获得模型输出的“得分”和“理由”，极大地缩短了人工评估的周期。

------

## 三、 可观测性与监控

系统不仅关注提示词的内容，还关注其运行时的性能。每次渲染都会记录在 `RenderLog` 表中：

- **Token 计数**：使用 `tiktoken` 准确计算 GPT-4 标准下的 Token 消耗。
- **延迟监控**：毫秒级的渲染耗时统计（`latency_ms`）。

这些数据通过 `/v1/stats` 接口汇总，为后续的成本优化和性能调优提供了依据。

------

## 四、 快速部署与开发

为了方便开发者快速上手，项目完全容器化。Dockerfile 使用了轻量级的 `python:3.10-slim` 基础镜像 4，并针对国内环境优化了 pip 源 5。



使用 Docker Compose 即可一键启动：

Bash

```
docker-compose up -d
```

系统通过挂载卷（Volumes）实现了代码的热重载（`--reload`）和 SQLite 数据的持久化，兼顾了开发体验与数据安全。

------

## 五、 结语与展望

从硬编码到平台化管理，是 LLM 应用走向成熟的必经之路。该平台目前已初步具备了提示词生命周期管理的能力。

**未来的演进方向：**

1. **集成 Playground**：在 Web 端直接调试并对比不同版本的渲染效果。
2. **更复杂的实验设计**：支持多变量测试（Multivariate Testing）。
3. **自动版本回滚**：当 LLM Judge 评分低于阈值时，自动切回稳定版。

通过这套系统，团队可以将更多精力集中在提示词工程（Prompt Engineering）本身，而非琐碎的代码维护。

------

