---
title: 深度解析：LangChain 的“回归”之路 —— 为什么我们不再执着于复杂的图结构？
date: 2025-12-26 11:36:24
tags:
- AI Infra LangChain LangGraph
categories:
- LangChain 
---



------

# 深度解析：LangChain 的“回归”之路 —— 为什么我们不再执着于复杂的图结构？



### 引言：消失的“链”与重生的“图”

在 LLM 应用开发的短短两年里，开发者经历了从初见 LangChain `Chain` 模式的惊艳，到面对复杂业务时对其“黑盒”化、线性结构的痛苦，再到 LangChain 官方强推 LangGraph 解决循环问题的狂热。

但最近，社区出现了一种明显的声音：**“我们好像又用回 LangChain 了。”** 这不是技术倒退，而是一场关于**抽象层级（Abstraction Layer）**的理性回归。

------

## 一、 证据链：从源码与社区讨论看“回归”

### 1. 源码证据：`langchain-core` 的扩张与预置 Agent 的“平民化”

在早期，如果你想做一个具备循环思考能力的 Agent，官方文档会引导你使用 `LangGraph` 构建节点。但观察最近的源码变动：

- 证据 A：create_tool_calling_agent 的崛起

  在 langchain.agents 模块中，官方引入了更直接的工具调用接口。它不再强制你定义一个图（Graph），而是通过核心库中的 Runnable 序列直接实现。

- 证据 B：langgraph.prebuilt 的功能反哺

  原本属于 LangGraph 的 create_react_agent 等高级功能，现在在 LangChain 的示例代码中被作为首选。这意味着官方正在模糊“简单 Agent”与“复杂图”之间的界限。

### 2. 社区证据：针对“过度工程”的反思

在 Hacker News 和 Reddit 上，针对 LangChain 过于复杂的讨论（著名的 **"LangChain is a Bridge to Nowhere"**）倒逼了团队做出改变。

> **Reddit 用户意见：** "I don't need a state machine (LangGraph) just to ask a model to call a function and loop twice. I just need a cleaner pipe."

------

## 二、 核心矛盾：为什么会有这种“反复”？

为了理解这种回归，我们需要对比三个阶段的代码逻辑演进：

### 阶段 1：传统 Chain（过于僵硬）

```Python
# 传统的线性链，难以处理循环和重试
chain = LLMChain(llm=llm, prompt=prompt)
res = chain.run("input") 
```

### 阶段 2：LangGraph 时代（强大但沉重）

为了解决循环，开发者不得不编写大量的 `State` 定义和 `Nodes`：

```Python
# 需要定义状态、节点、边、编译图
workflow = StateGraph(AgentState)
workflow.add_node("agent", call_model)
workflow.add_edge("agent", "action")
app = workflow.compile()
```

**痛点：** 对于 80% 的中等复杂度任务，这种模式显得过于“重”，学习曲线陡峭。

### 阶段 3：现代 LangChain / LCEL（螺旋式上升）

现在的“回归”是基于 **LCEL (LangChain Expression Language)** 的。它保留了图的灵活性，但回到了链的简洁感：

```Python
# 现在的写法：逻辑清晰且支持动态路由
agent_chain = prompt | model | parser
# 通过内建的工具调用和 bind 逻辑，无需显式画图即可实现 Agent
```

------

## 三、 深度讨论：为什么“回归”是必然的？

### 1. 开发者体验（DX）的修正

LangGraph 本质上是一个**分布式状态机**。虽然它解决了 Long-running tasks（长耗时任务）的问题，但大多数开发者只是在写对话插件。强迫开发者去理解“节点”和“边”，降低了开发效率。

### 2. LCEL 的成熟

LangChain 团队投入了巨大精力优化 LCEL。当 LCEL 能够原生支持 `bind_tools`、流式输出（Streaming）和异步处理时，LangGraph 很多底层的控制逻辑可以被封装成一个个简单的“链式符号”。

### 3. 架构分层：从“替代”到“插件化”

现在的趋势是：

- **LangChain** 是你的**“编程语言”**（负责逻辑编排）。
- **LangGraph** 是你的**“高级库”**（只在需要极致状态控制、人工干预、超长任务流时调用）。

------

## 四、 结论：我们该如何选择？

这场“回归”本质上是 **“简单任务回归简单工具”**。

| **任务类型**                   | **建议方案**                    | **原因**                            |
| ------------------------------ | ------------------------------- | ----------------------------------- |
| 基础 RAG / 简单对话            | **LangChain (LCEL)**            | 开发快，易于维护                    |
| 标准 Agent（工具调用）         | **LangChain (Prebuilt Agents)** | 无需理解图结构即可实现循环          |
| 复杂多智能体协作 (Multi-Agent) | **LangGraph**                   | 必须精细控制不同 Agent 间的状态传递 |
| 需要“断点重试”的长业务流       | **LangGraph**                   | 图结构提供的持久化状态是不可替代的  |

### 结语

LangChain 的演进证明了：**最优秀的技术框架，最终都会在“强大的控制力”与“极简的表达力”之间找到平衡。** 现在的“回归”，正是这种平衡达成的标志。
