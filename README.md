# Harness Engineering 实施指南

> **目标**：构建完整的 AI Agent 运行时基础设施，实现可靠、可扩展、可维护的 AI 驱动开发工作流。

---

## 📖 目录

1. [核心概念](#核心概念)
2. [架构设计](#架构设计)
3. [工具选型](#工具选型)
4. [实施步骤](#实施步骤)
5. [最佳实践](#最佳实践)
6. [案例研究](#案例研究)
7. [故障排查](#故障排查)
8. [更新日志](#更新日志)

---

## 核心概念

### Harness Engineering（脚手架工程）

**定义**：设计和构建让 AI Agent 可靠运行的完整运行时基础设施。

**核心组件**：
- 🤖 **Sub-Agent System** - 子代理调度系统
- 🏖️ **Sandbox** - 沙箱执行环境
- 🧠 **Memory** - 记忆系统
- 🛠️ **Skills/Tools** - 技能/工具集成
- 🔄 **Lifecycle Management** - 生命周期管理

### Context Engineering（上下文工程）

**定义**：管理 Agent 运行时可用的信息，确保正确信息在正确时机出现。

**核心技术**：
- 📦 **Context Isolation** - 上下文隔离
- 📥 **Progressive Loading** - 渐进加载
- 📝 **Summarization** - 自动摘要
- 🔍 **Memory Retrieval** - 记忆检索

### Prompt Engineering（提示词工程）

**定义**：设计和优化输入给 LLM 的文本提示，以获得期望输出。

**核心技术**：
- 📋 **System Prompt** - 系统提示
- 🎯 **Few-shot** - 少样本学习
- 🔗 **Chain-of-Thought** - 思维链
- 📊 **Output Formatting** - 输出格式化

**层次关系**：
```
Harness Engineering (最上层)
    └─> Context Engineering
            └─> Prompt Engineering (最底层)
```

---

## 架构设计

（待完善）

---

## 工具选型

（待完善）

---

## 实施步骤

（待完善）

---

## 最佳实践

（待完善）

---

## 案例研究

（待完善）

---

## 故障排查

（待完善）

---

## 更新日志

### 2026-04-08 - Anthropic 三 Agent 架构深度解析 & HumanLayer 四杠杆模型

**更新**：

1. **Anthropic 长时运行 Harness 设计深度解析**
   - 📝 来源：[Anthropic - Harness Design for Long-Running App Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
   - 🎯 三 Agent Harness（初始化器 + 编码器 + 上下文交接）官方详解
   - 💡 Opus 4.6 显著提升规划能力和长上下文检索

2. **OpenAI Harness Engineering 方法论详解**
   - 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
   - 🎯 机器可读制品驱动 Agent 可靠执行
   - 💡 从空仓库起步、Agent 可读性、执行计划三大关键实践

3. **HumanLayer：Harness 作为 Context Engineering 子集**
   - 📝 来源：[HumanLayer - Skill Issue](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
   - 🎯 四杠杆模型：系统提示、工具/MCP、子 Agent、钩子
   - 💡 Claude Code 的 Explore/Bash 子 Agent 上下文封装分析

4. **Sud Shekhar：自主 Agent 的 Context Engineering**
   - 📝 来源：[Sud Shekhar - From Prompts to Context](https://www.sudshekhar.com/blog/from-prompts-to-context-mastering-context-engineering-for-autonomous-ai-agents)
   - 🎯 从静态提示到动态上下文到完整 Harness 的演进
   - 💡 Entry Control → Runtime Management → Knowledge Layer 三层架构

5. **框架版本更新**
   - Superpowers — **139,330** ⭐ 🚀（+4,159）— 逼近 140K
   - DeerFlow — **59,117** ⭐ — 循环检测哈希优化
   - CrewAI — **48,278** ⭐ — 线程安全修复
   - BMAD-METHOD — **43,896** ⭐ — installer 品牌化
   - LangGraph — **28,647** ⭐ — uv workspace 支持
   - OpenAI Agents SDK — **20,633** ⭐ — HoneyHive 追踪集成
   - Google ADK — **18,797** ⭐ — Trigger 端点

**更新文件**：
- `02-tools.md` — 新增 2026-04-08 框架版本迭代速报（7 个框架全部更新）
- `04-best-practices.md` — 新增 #29 HumanLayer 四杠杆模型、#30 Sud Shekhar Context Engineering
- `05-case-studies.md` — 新增 Anthropic 三 Agent 深度解析、OpenAI 方法论详解、HumanLayer 分析、框架增长数据

**关键洞察**：
- 🏗️ **三 Agent 架构验证**：Anthropic 的初始化器+编码器+上下文交接模式解决长时运行退化问题
- 📐 **四杠杆模型**：HumanLayer 提供了 Harness Engineering 的简洁理论框架
- 🔧 **机器可读制品**：OpenAI 强调将人类知识转化为 Agent 可消费的格式
- 🚀 **Superpowers 139K**：逼近 140K 里程碑，单周增长超 4K

---

### 2026-04-05 - MCP+A2A 双协议时代 & Mem0 记忆驱动 Context Engineering

**更新**：

1. **MCP+A2A 双协议成为 Agent 互操作性标准**
   - 🔥 LangGraph、CrewAI、Google ADK 相继支持 MCP+A2A 双协议
   - 💡 CrewAI 拥有三种 MCP 传输机制（Stdio、SSE、Streamable HTTPS），集成深度领先
   - 📌 框架间 Agent 协作能力大幅提升，互操作性进入新阶段

2. **Mem0 Context Engineering 完整指南**
   - 📝 来源：[Mem0 - Context Engineering for AI Agents: Complete Guide](https://mem0.ai/blog/context-engineering-ai-agents-guide)
   - 🎯 从记忆系统角度定义 Context Engineering：结构化上下文和记忆使 AI 系统随时间智能行为
   - 💡 核心支柱：记忆管理（持久化+语义检索）、RAG 增强（检索+压缩）、智能格式化（精确时机）
   - 🏗️ 记忆压缩引擎：写入→语义搜索→智能压缩→记忆类型隔离

3. **框架版本更新**
   - Superpowers — **135,171** ⭐ 🚀（+1,145）— 突破 135K
   - DeerFlow — **57,803** ⭐ — 并发线程修复、附件上传修复
   - CrewAI — **48,034** ⭐ — 突破 48K，MCP+A2A 双协议
   - BMAD-METHOD — **43,565** ⭐ — 贡献规范化
   - LangGraph — **28,419** ⭐ — MCP+A2A 双协议支持
   - OpenAI Agents SDK — **20,570** ⭐ — 公共 flush_traces API
   - Google ADK — **18,741** ⭐ — 凭证自动刷新、BashTool 资源限制

**更新文件**：
- `02-tools.md` — 新增 2026-04-05 框架版本迭代速报（7 个框架全部更新，突出 MCP+A2A 双协议）
- `04-best-practices.md` — 新增 #28 Mem0 Context Engineering 完整指南（记忆压缩引擎架构、与 Phil Schmid 方法对比）
- `05-case-studies.md` — 新增 2026-04-05 补充（MCP+A2A 双协议时代分析、Mem0 记忆驱动 Context Engineering、框架增长数据）

**关键洞察**：
- 🔗 **MCP+A2A 双协议标准**：2026 年 Agent 互操作性进入新阶段，框架间协作能力大幅提升
- 🧠 **记忆即上下文**：Mem0 的记忆压缩引擎与 Phil Schmid 的信息流架构形成互补
- 🚀 **Superpowers 135K**：继续保持最快增长速度

---

### 2026-04-04 - 行业标准化里程碑 & 上下文瓶颈新认知

**更新**：

1. **Anthropic 发布《2026 Agentic Coding Trends Report》**
   - 📝 来源：[Anthropic - Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)
   - 🎯 八大趋势：单 Agent→协调团队、长时运行 Agent、Agentic QA 标准化、安全优先架构
   - 💡 Agentic QA（AI 审查 AI）成为 2026 标准实践

2. **Linux Foundation 成立 Agentic AI Foundation (AAIF)**
   - 📝 来源：[Linux Foundation - AAIF](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
   - 🔥 MCP、goose、AGENTS.md 作为创始项目入驻
   - 💡 MCP 被比喻为"AI 的 USB-C 接口"

3. **OpenAI Harness Engineering 深度实践（Ryan Lopopolo）**
   - 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
   - 🎯 "零手写代码"约束下构建完整 Harness：自定义 linter、结构测试、Ralph Wiggum Loop
   - 💡 核心理念："每次发现 Agent 犯错，就工程化一个方案让它永远不再犯"

4. **Epsilla：AI 工程三阶段演进论**
   - 📝 来源：[Epsilla Blog](https://www.epsilla.com/blogs/harness-engineering-evolution-prompt-context-autonomous-agents)
   - 🎯 Prompting → Context Engineering → Harness Engineering 三阶段模型
   - 💡 "Agent 不难，Harness 难" + 语义图谱实现 Agent-as-a-Service

5. **Context is AI Coding's Real Bottleneck (The New Stack)**
   - 📝 来源：[The New Stack](https://thenewstack.io/context-is-ai-codings-real-bottleneck-in-2026/)
   - 🎯 上下文是 AI 编码的真正瓶颈，不是模型能力
   - 💡 阅读 AI 代码需要"从输出逆向工程意图"的新认知模式

6. **框架版本更新**
   - Superpowers — **134,026** ⭐ 🚀（+1,840）— 突破 134K
   - DeerFlow — **57,301** ⭐ — 文档上下文注入 (#1738)
   - CrewAI — **47,957** ⭐ — AMP Training Tab
   - BMAD-METHOD — **43,485** ⭐ — context-aware planning (#2185)
   - LangGraph — **28,356** ⭐ — v1.1.6
   - OpenAI Agents SDK — **20,551** ⭐ — vLLM 兼容
   - Google ADK — **18,730** ⭐ — Easy GCP

**更新文件**：
- `02-tools.md` — 新增 2026-04-04 框架版本迭代速报（7 个框架全部更新）
- `05-case-studies.md` — 新增 Anthropic 趋势报告、AAIF 成立、OpenAI 深度实践、Epsilla 演进论、The New Stack 瓶颈分析

**关键洞察**：
- 🏛️ **行业标准化里程碑**：Linux Foundation AAIF 成立标志着 Agent 技术进入标准化阶段
- 📊 **Agentic QA 标准化**：Anthropic 趋势报告确认 AI 审查 AI 已成生产标准
- 🔧 **零手写代码理念**：OpenAI 内部实践公开化，Ralph Wiggum Loop 成为社区参考模式
- 🧠 **上下文是真正瓶颈**：认知模式转变——从"模型不够强"到"上下文管理不够好"
- 🚀 **Superpowers 134K**：持续保持最快增长速度

---

### 2026-04-03 - Context Rot 解决方案 & 框架新里程碑

**更新**：

1. **Phil Schmid Context Engineering Part 2：Context Rot 深度解决方案**
   - 📝 来源：[Phil Schmid - Context Engineering Part 2](https://www.philschmid.de/context-engineering-part-2)
   - 🎯 深入探讨 Context Rot（上下文腐烂）及前沿解决方案
   - 💡 Context Compaction 可逆化、子代理上下文隔离、分层路由策略

2. **Anthropic Effective Context Engineering 核心要点补充**
   - 📝 来源：[Anthropic - Effective Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
   - 🎯 Context Engineering 管理全部上下文状态，不仅是 Prompt
   - 💡 Tool Result Clearing 是最轻量的上下文压缩手段

3. **State of Context Engineering 2026：60% 利用率阈值**
   - 📝 来源：[State of Context Engineering 2026 (Medium)](https://medium.com/@kushalbanda/state-of-context-engineering-in-2026-cf92d010eab1)
   - 🎯 上下文利用率超过 60% 时的优先级处理策略
   - 💡 生产环境分层叠加：Entry Control → Runtime Management → Knowledge Layer

4. **框架版本更新**
   - Superpowers — **132,186** ⭐ 🚀（+1,956）— 突破 132K
   - DeerFlow — **56,695** ⭐ — 文档站点上线
   - CrewAI — **47,865** ⭐ — **v1.13.0 正式发布** 🎉
   - BMAD-METHOD — **43,365** ⭐ — CI 质量检查
   - LangGraph — **28,265** ⭐ — deploy revisions list
   - OpenAI Agents SDK — **20,521** ⭐ — v0.13.4
   - Google ADK — **18,714** ⭐ — gemini-3.1-flash-live-preview

5. **热门框架动态（2026-04-03 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) — **132,186** ⭐ 🚀（+1,956）
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **56,695** ⭐ 🚀（+677）
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,865** ⭐ — v1.13.0 正式发布
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,365** ⭐
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,265** ⭐
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,521** ⭐
   - [google/adk-python](https://github.com/google/adk-python) — **18,714** ⭐

**更新文件**：
- `02-tools.md` — 新增 2026-04-03 框架版本迭代速报（Superpowers 132K、CrewAI v1.13.0 正式版）
- `04-best-practices.md` — 新增 Phil Schmid Context Rot 解决方案、60% 利用率阈值管理
- `05-case-studies.md` — 新增 Phil Schmid Part 2 分析、Anthropic 补充、框架增长数据

**关键洞察**：
- 🧠 **Context Rot 前沿解法**：可逆压缩和子代理隔离是解决上下文腐烂的核心技术
- 📊 **60% 利用率规则**：Context Engineering 从定性建议走向定量工程
- 🚀 **CrewAI v1.13.0 正式发布**：多 Agent 协作框架进入稳定成熟期
- 📈 **Superpowers 132K**：持续保持最快增长速度，生态系统进一步扩大

---

### 2026-04-02 - Context Engineering 认知升级 & Superpowers 突破 130K

**更新**：

1. **Anthropic Harness 设计迭代经验**
   - 📝 来源：[Anthropic - Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
   - 🎯 从双 Agent 架构简化为更统一的结构
   - 💡 Harness 设计本身需要持续迭代，像软件一样持续重构

2. **LangChain Context Engineering 四大策略框架**
   - 📝 来源：[LangChain Blog - Context Engineering for Agents](https://blog.langchain.com/context-engineering-for-agents/)
   - 🎯 Write / Select / Compress / Isolate 四大策略桶走向标准化
   - 💡 结合 LangGraph 提供跨会话持久化上下文的生产级方案

3. **Context Engineering 2026 三大认知颠覆**
   - 📝 来源：[The AI Corner - Context Engineering Guide 2026](https://www.the-ai-corner.com/p/context-engineering-guide-2026)
   - ⚡ "Think step by step" 对推理模型有害
   - ⚡ 长 prompt 超 3000 token 开始降性能
   - ⚡ Few-shot CoT 仅剩格式对齐
   - 💡 替代方案：Prompt-as-Code + 6 组件架构 + 4 层防御策略

4. **htek.dev Harness 原型实践**
   - 📝 来源：[Agent Harnesses: Why 2026 Isn't About More Agents](https://htek.dev/articles/agent-harnesses-controlling-ai-agents-2026/)
   - 🎯 实际构建 Harness 原型：工具拦截、预算控制、迭代循环
   - 💡 2026 年的挑战是控制 Agent 的基础设施，而非构建更多 Agent

5. **框架版本更新**
   - Superpowers — **130,230** ⭐ 🎉 突破 130K 里程碑
   - DeerFlow — **56,018** ⭐ 网关层稳定性修复
   - CrewAI — **47,789** ⭐ A2UI 扩展 + GPT-5 多模态 vision 支持
   - OpenAI Agents SDK — **20,494** ⭐ v0.13.4 approval policies
   - BMAD-METHOD — **43,229** ⭐ checkpoint-preview skill

6. **热门框架动态（2026-04-02 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) — **130,230** ⭐ 🚀（+2,090）— 突破 130K 🎉
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **56,018** ⭐ 🚀（+781）
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,789** ⭐ — A2UI + GPT-5
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,229** ⭐
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,160** ⭐
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,494** ⭐ — v0.13.4
   - [google/adk-python](https://github.com/google/adk-python) — **18,694** ⭐

**更新文件**：
- `02-tools.md` — 新增 2026-04-02 框架版本迭代速报（Superpowers 突破 130K）
- `04-best-practices.md` — 新增 LangChain 四大策略桶、Context Engineering 认知转变、Prompt-as-Code 方法论、Harness 原型实践
- `05-case-studies.md` — 新增 Anthropic 迭代经验、NxCode 指南、Context Engineering 认知颠覆

**关键洞察**：
- 🧠 **Context Engineering 认知升级**：2025 年的很多"最佳实践"（如 CoT、长 prompt）在推理模型时代已过时
- 📐 **四大策略桶标准化**：Write/Select/Compress/Isolate 成为 Context Engineering 的共识框架
- 🏗️ **Harness 需要迭代**：Anthropic 证实 Harness 架构本身需要像软件一样持续重构
- 🚀 **Superpowers 130K**：Agent 技能框架生态系统持续扩大
- 🔒 **Approval Policies**：OpenAI Agents SDK 引入工具调用审批机制，安全控制走向细粒度

---

### 2026-04-01 - LangChain Harness 实战标杆 & Superpowers 跨 Agent 平台扩展

**更新**：

1. **LangChain DeepAgents Harness Engineering 实战**
   - 📝 来源：[LangChain Blog - Improving Deep Agents with Harness Engineering](https://blog.langchain.com/improving-deep-agents-with-harness-engineering/)
   - 🎯 TerminalBench 2.0 分数从 52.8 → 66.5（+13.7 分），不换模型仅优化脚手架
   - 💡 引入 Ralph Wiggum Loop 验证循环和 Multi-model Harness 概念

2. **Phil Schmid：Agent Harness 是 2026 年最重要的基础设施**
   - 📝 来源：[Phil Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)
   - 🎯 Harness 不是 Agent 本身，而是管理长周期任务的基础设施层
   - 💡 "好模型配差 Harness 不如差模型配好 Harness"

3. **Comet Context Engineering 可观测性实践**
   - 📝 来源：[Comet - Context Engineering](https://www.comet.com/site/blog/context-engineering/)
   - 🎯 访问和溯源管理：每个上下文元素的所有权、来源、版本历史
   - 💡 推荐 Opik 等工具实现完整调用链追踪

4. **Superpowers v5.0.7 新增 GitHub Copilot CLI 支持**
   - 🆕 SessionStart 上下文注入、Claude Code → Copilot CLI 工具映射
   - 128,140 ⭐（+2,524），跨编码 Agent 通用技能定位强化

5. **框架版本更新**
   - LangGraph v1.1.4 — 递归限制修复 + LangSmith 元数据
   - OpenAI Agents SDK v0.13.3 — 服务端会话管理修复
   - DeerFlow — Sandbox 并发序列化和跨平台路径兼容性修复
   - CrewAI v1.13.0a4 alpha — v1.13 稳定版即将到来

6. **热门框架动态（2026-04-01 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) — **128,140** ⭐ 🚀（+2,524）— Copilot CLI 支持
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **55,237** ⭐ 🚀（+1,206）— Sandbox 稳定性
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,726** ⭐ — v1.13.0a4
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,099** ⭐ — 技能设计优化
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,068** ⭐ — v1.1.4
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,463** ⭐ — v0.13.3
   - [google/adk-python](https://github.com/google/adk-python) — **18,687** ⭐ — 安全加固

**更新文件**：
- `02-tools.md` — 新增 2026-04-01 框架版本迭代速报（全部 star 数据更新）
- `04-best-practices.md` — 新增 LangChain DeepAgents 实战、Context Engineering 可观测性实践
- `05-case-studies.md` — 新增 LangChain Harness 实战、Phil Schmid Agent Harness 定义、Superpowers 跨平台扩展

**关键洞察**：
- 📊 **Harness 优化效果量化**：LangChain 实战证明仅优化脚手架即可提升 Agent 13.7 分
- 🏗️ **Harness = 操作系统**：Phil Schmid 明确定位 Harness 为 Agent 的基础设施层
- 🔭 **可观测性成为关键**：Context Engineering 需要完整的调用链追踪和溯源管理
- 🔌 **跨 Agent 技能标准化**：Superpowers 扩展至 GitHub Copilot CLI，通用技能生态成型
- 🚀 **Superpowers 128K**：持续保持最快增长速度

---

### 2026-03-31 - Martin Fowler 深度点评 & 框架生态持续增长

**更新**：

1. **Martin Fowler 深度点评 Harness Engineering**
   - 📝 来源：[Martin Fowler - Exploring Gen AI: Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)
   - 🎯 探讨自定义 lint 规则、结构性测试、知识文档是否成为新一代服务模板
   - 💡 提出代码库设计模式的"Harness 化"是否会成为新的抽象层

2. **Phil Schmid：Context Engineering 是独立学科，不是 Prompting**
   - 📝 来源：[Phil Schmid - Context Engineering](https://www.philschmid.de/context-engineering)
   - 🎯 Context = 对话历史 + 工具定义 + 检索文档 + 用户画像 + 系统指令的完整数据环境
   - 💡 关键策略：上下文压缩、状态卸载、子代理隔离、动态上下文组装

3. **Fast.io：Context Engineering 防止"上下文腐烂"**
   - 📝 来源：[Context Engineering for AI Agents (Fast.io)](https://fast.io/resources/context-engineering-ai-agents/)
   - 🎯 "Context Rot"（上下文腐烂）概念：对话过长导致模型丢失主要目标
   - 💡 预算化上下文窗口、压缩历史对话、MCP 标准协议、Task-Agent 模式

4. **Google ADK 2.0 Alpha 重大更新**
   - 🔥 引入确定性工作流和图式编排
   - 🆕 LocalEnvironment（本地命令执行和文件 I/O）
   - 🆕 EnvironmentToolset 支持
   - 🔌 A2A 协议和 MCP 集成

5. **热门框架动态（2026-03-31 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) — **125,616** ⭐ 🚀（+2,744）— 突破 125K
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **54,031** ⭐ 🚀（+1,539）— 突破 54K
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,617** ⭐ — v1.13.0rc1 大规模重构
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **42,981** ⭐ — v6.2.2
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **27,963** ⭐ — 持续依赖更新
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,426** ⭐ — v0.13.2 修复
   - [google/adk-python](https://github.com/google/adk-python) — **18,672** ⭐ — ADK 2.0 Alpha

**更新文件**：
- `02-tools.md` — 新增 2026-03-31 框架版本迭代速报（全部 star 数据更新）
- `04-best-practices.md` — 新增 Context Engineering 防止"上下文腐烂"最佳实践
- `05-case-studies.md` — 新增 Martin Fowler 深度点评、Phil Schmid Context Engineering、ADK 2.0 Alpha 分析

**关键洞察**：
- 🏛️ **Martin Fowler 深度参与**：从"背书"到"深度点评"，标志方法论成熟度进一步提升
- 📐 **Context Engineering 学科化**：Phil Schmid 明确定义为独立系统工程学科
- 🚀 **Superpowers 125K**：保持最快增长速度，生态系统持续扩大
- 🔧 **Google ADK 2.0 Alpha**：从轻量 SDK 演进为完整 Agent 编排平台

---

### 2026-03-30 - 行业转向确认 & 框架重大迭代

**重大更新**：

1. **Aakash Gupta 热文：2025 Was Agents, 2026 Is Agent Harnesses**
   - 📝 来源：[Medium - 2025 Was Agents. 2026 Is Agent Harnesses.](https://aakashgupta.medium.com/2025-was-agents-2026-is-agent-harnesses-heres-why-that-changes-everything-073e9877655e)
   - 🎯 核心论点：Agent 是引擎，Harness 是整车；行业焦点正式从"构建 Agent"转向"构建 Harness"
   - 💡 分析了对产品管理和技术架构的影响

2. **Context Engineering 六层上下文架构**
   - 📝 来源：[Context Engineering for AI Agents Guide](https://promptbuilder.cc/blog/context-engineering-agents-guide-2025)
   - 🎯 六层结构：System Instructions → Long-Term Memory → Retrieved Documents → Tool Definitions → Conversation History → Current Task
   - 💡 效果衡量指标：任务成功率、检索精度、工具使用准确率、用户满意度

3. **Context Engineering 系统工程视角**
   - 📝 来源：[Redis Blog - Context Engineering Best Practices](https://redis.io/blog/context-engineering-best-practices-for-an-emerging-discipline/)
   - 🔑 Karpathy："LLM 是新操作系统的内核进程"
   - 🔑 Lance Martin："LLM 是 CPU，上下文窗口是 RAM"
   - 💡 核心论点：没有记忆层的 Context Engineering 只是换了个名字的 Prompt Engineering

4. **DeerFlow 2.0 全面重写**
   - 📝 来源：[GitHub - bytedance/deer-flow](https://github.com/bytedance/deer-flow)
   - 🔥 与 v1 无代码共享，定位升级为 "Super Agent Harness"
   - 🏆 2026-02-28 登顶 GitHub Trending 第一名
   - 🐛 修复 artifacts API 存储型 XSS 漏洞

5. **Superpowers 进入 Claude 官方插件市场**
   - 📝 来源：[GitHub - obra/superpowers](https://github.com/obra/superpowers)
   - ⭐ **122,872** stars，最高 Agent 技能框架
   - 🎯 真正的红/绿 TDD 流程，支持 Claude Code、Cursor、Codex、OpenCode

6. **LangGraph 获评 2026 最佳 AI Agent 框架**
   - 📝 来源：多家独立评测
   - 🎯 持久化执行 + 综合记忆系统 + LangSmith 集成

7. **CrewAI AMP Suite 企业套件发布**
   - 🆕 Control Plane、追踪可观测性、统一管理平台、安全合规
   - 超 10 万开发者认证

8. **Google ADK 新特性**
   - 🆕 Session Rewind、Service Registry、Agent Config（无代码配置）、Tool Confirmation（HITL）

9. **热门框架动态（2026-03-30 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) - **122,872** ⭐ 🚀 - 进入 Claude 官方市场
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - **52,492** ⭐ 🚀 - 2.0 全面重写
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - **47,510** ⭐ - AMP Suite 企业套件
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - **42,823** ⭐ - V6 发布
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - **27,869** ⭐ - 2026 最佳框架
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - **20,402** ⭐ - 100+ LLM 支持
   - [google/adk-python](https://github.com/google/adk-python) - **18,654** ⭐ - Session Rewind 等新特性

**更新文件**：
- `02-tools.md` — 新增 2026-03-30 框架版本迭代速报（全部 star 数据更新 + 新特性）
- `04-best-practices.md` — 新增六层上下文架构、系统工程视角（Karpathy/Lance Martin 类比）
- `05-case-studies.md` — 新增行业转向分析、DeerFlow 2.0 重写、Superpowers 里程碑

**关键洞察**：
- 🔄 **行业转向确认**：从"构建 Agent"到"构建 Harness"成为广泛共识
- 🏗️ **DeerFlow 2.0 重写**：Super Agent Harness 从实验框架走向生产平台
- 📐 **Context Engineering 理论深化**：Karpathy/Lance Martin 等权威类比推动概念普及
- 🚀 **Superpowers 里程碑**：进入 Claude 官方市场，标准化技能分发渠道形成
- 📊 **框架生态持续增长**：Superpowers 122K+、DeerFlow 52K+、BMAD V6 发布

---

**重大更新**：

1. **OpenAI 正式发布 Harness Engineering 方法论**
   - 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
   - 🎯 新增核心概念：Agent Legibility、Repository Knowledge as System of Record、Execution Plans
   - 👤 Martin Fowler 公开背书
   - 💡 工程师角色从代码编写者转变为环境设计者

2. **Anthropic Context Engineering 官方最佳实践**
   - 📝 来源：[Anthropic - Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
   - 🔑 五大上下文来源管理：System Instructions、Tool Definitions、MCP、External Data、Message History
   - 💡 关键策略：Tool Result Clearing、Compaction、Progressive Disclosure
   - 🆕 Memory Tool 公测版发布

3. **Context Engineering 六大核心技术实战版**
   - 📝 来源：[Context Engineering 6 Techniques](https://pub.towardsai.net/context-engineering-the-6-techniques-that-actually-matter-in-2026-90bb0272ae85)
   - 🎯 技术矩阵：Progressive Disclosure / Compression / Routing / Evolved Retrieval / Tool Management / Evaluation
   - 💡 分层架构：Entry Control → Runtime Management → Knowledge Layer → Feedback Loop

4. **MCP 成为 Agent-Tool 连接标准**
   - 📝 来源：[State of Context Engineering in 2026](https://medium.com/@kushalbanda/state-of-context-engineering-in-2026-cf92d010eab1)
   - 🎯 MCP 被 Anthropic、OpenAI、Google 等主要厂商采纳
   - ⚠️ 核心挑战：累积 Action History 挤占 System Instructions 和 Tool Definitions 空间

5. **主流媒体关注 Harness Engineering**
   - 📝 来源：[Chosun - Korea Daily](https://www.chosun.com/english/industry-en/2026/03/26/QEFHIB7ANJFC5A77U3ITLMGHQY/)
   - 📝 来源：[NXCode Complete Guide](https://www.nxcode.io/resources/news/harness-engineering-complete-guide-ai-agent-codex-2026)
   - 🎯 Harness Engineering 获得韩国主流媒体报道，概念走向大众

6. **热门框架动态（2026-03-29 数据）**
   - [obra/superpowers](https://github.com/obra/superpowers) - **120,748** ⭐ 🚀 - Inline Self-Review 替代 Subagent Review Loops，执行时间减少 25 分钟
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - **51,257** ⭐ 🚀 - 突破 51K！lead_agent Channel Assistant ID 支持
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - **47,428** ⭐ - v1.13.0rc1
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - **42,712** ⭐ - v6.2.2 依赖图重构
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - **27,782** ⭐ - CLI v0.4.19
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - **20,375** ⭐ - v0.13.2
   - [google/adk-python](https://github.com/google/adk-python) - **18,643** ⭐ - v2.0.0-alpha.2

**更新文件**：
- `01-architecture.md` — 新增 OpenAI 官方 Harness Engineering 核心概念章节
- `02-tools.md` — 更新框架 star 数据和版本迭代速报
- `04-best-practices.md` — 新增 Context Engineering 官方最佳实践、六大核心技术、MCP 标准化
- `05-case-studies.md` — 新增主流媒体关注和框架里程碑分析

**关键洞察**：
- 🏛️ **方法论官方化**：OpenAI 正式发布 Harness Engineering 方法论，Martin Fowler 背书
- 📐 **Context Engineering 标准化**：Anthropic 发布官方指南，六大核心技术框架成型
- 🔌 **MCP 标准确立**：Model Context Protocol 成为 Agent-Tool 连接的事实标准
- 📰 **主流媒体关注**：Harness Engineering 从技术圈走向大众视野
- 🚀 **Superpowers 里程碑**：突破 120K stars，Self-Review 模式革新减少编排开销

---

### 2026-03-28 (下午) - 理论体系深化 & 框架版本迭代

**重大更新**：

1. **Epsilla: "Agents aren't hard; the Harness is hard."**
   - 📝 来源：[Epsilla Blog - Harness Engineering Evolution](https://www.epsilla.com/blogs/harness-engineering-evolution-prompt-context-autonomous-agents) (Mar 2026)
   - 🔑 核心论点得到 OpenAI 和 Anthropic 验证：通过规则、反馈循环和 linter 约束 agent 的解空间，反而提升生产力和可靠性
   - 💡 Harness Engineering 构建围绕 AI agent 的完整运行环境，在更高层次上架构规则、反馈和基础设施系统，防止 agent 犯错

2. **Agent Engineering: Harness Engineering 正式定义**
   - 📝 来源：[Agent Engineering - Harness Engineering in 2026](https://www.agent-engineering.dev/article/harness-engineering-in-2026-the-discipline-that-makes-ai-agents-production-ready)
   - 🎯 正式定义：设计**约束、工具、反馈循环、文档和验证系统**的学科，引导 AI agent 产出可靠、可维护、可扩展的软件
   - 📜 历史脉络：2025 年底 Mitchell Hashimoto 讨论 → 2026 年 OpenAI 内部实验正式化
   - ⚠️ 解决核心痛点：纯上下文工程的局限——agent 在长时间运行任务中仍会漂移、积累"熵"

3. **Phil Schmid: Agent Harness 的苦涩教训**
   - 📝 来源：[Phil Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)
   - 🎯 Harness 将模糊的多步骤 agent 工作流转化为结构化数据来记录和评分
   - 💡 关键技术：上下文压缩、状态卸载到存储、将任务隔离到子 agent

4. **Cobus Greyling: AI Harness Engineering 崛起**
   - 📝 来源：[The Rise of AI Harness Engineering](https://cobusgreyling.medium.com/the-rise-of-ai-harness-engineering-5f5220de393e)
   - 🎯 Harness Engineering 是决定 AI Agent 是否真正能在生产环境中工作的缺失架构层

5. **Deloitte 2026 技术预测：AI Agent 编排**
   - 📝 来源：[Deloitte - Unlocking Exponential Value with AI Agent Orchestration](https://www.deloitte.com/us/en/insights/industry/technology/technology-media-and-telecom-predictions/2026/ai-agent-orchestration.html)
   - 🔑 德勤明确将"AI agent 编排"列为关键解锁
   - 💡 工作流变得模块化并由 agent 驱动
   - 📊 2026 年 AI agent 将在不同编程语言、框架、基础设施和通信协议中扩散

6. **多 Agent 编排框架对比（2026 最新）**
   - 📝 来源：[Best Multi-Agent Frameworks 2026](https://gurusup.com/blog/best-multi-agent-frameworks-2026)
   - 🎯 对比 6 大框架：OpenAI Agents SDK、LangGraph、CrewAI、AutoGen/AG2、Google ADK 和 Claude Agent SDK
   - 💡 选择建议：不同框架适用不同场景，需要根据项目需求选择

7. **热门框架动态（2026-03-28 下午数据）**
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - **50,454** ⭐ 🚀 - 首次突破 50K！
     - 🐛 MCP filesystem 路径暴露问题（#1452）
     - 🔧 Memory 管理/清除功能请求（#1333）
     - 🛠️ ViewImageMiddleware fallback 修复（#1336）
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - **27,711** ⭐
     - 📦 `langgraph==1.1.3` (2026-03-18)
     - 🔒 安全更新：cryptography 46.0.5 → 46.0.6
     - 📝 新增威胁模型文档（#7026）
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - **47,387** ⭐
     - 📦 `1.13.0rc1` (2026-03-27)
     - 🔒 XXE 防护：xml.etree.ElementTree → defusedxml（#4967）
     - 🐛 循环检测 off-by-one 修复（#5034）
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - **20,361** ⭐
     - 📦 `v0.13.2` (2026-03-26)
     - 🐛 Prompt caching 在 base64 输入时降至 0%（#2784）
     - 🔧 流式工具执行 guardrail 修复（#2688）
   - [google/adk-python](https://github.com/google/adk-python) - **18,629** ⭐
     - 📦 `v2.0.0a2` (alpha, 2026-03-27) + `v1.28.0` (stable, 2026-03-26)
     - 🔒 MCP 集成缺少消息级认证和完整性验证（#4840）
     - ⚠️ 多 agent 导致应用启动变慢（#4870）

**关键洞察**：
- 📐 **理论体系成熟**：Harness Engineering 从实践经验上升为正式学科定义
- 🏗️ **行业共识形成**：Epsilla、Phil Schmid、Cobus Greyling 等多方观点趋于一致
- 📊 **德勤背书**：AI Agent 编排被列入 2026 关键技术预测
- 🚀 **DeerFlow 里程碑**：字节跳动 SuperAgent Harness 首次突破 50K stars
- 🔒 **安全意识提升**：多个框架集中修复安全漏洞（XXE、加密库、MCP 认证）

---

### 2026-03-28 (上午) - 基础设施噪声量化 & 框架生态动态

**重大更新**：

1. **Anthropic: 基础设施噪声对 AI 编码评估的影响研究**
   - 📝 来源：[Anthropic - Quantifying infrastructure noise in agentic coding evals](https://www.anthropic.com/engineering/quantifying-infrastructure-noise) (Mar 28, 2026)
   - ⚠️ 关键发现：基础设施配置可使 AI 编码基准测试波动几个百分点
   - 💡 技术建议：指定资源的 guaranteed allocation 和 hard kill threshold
   - 🎯 实证数据：3x ceiling 可将错误率从 5.8% 降到 2.1%
   - 🔍 排名榜启示：差距 < 3% 需要质疑配置一致性

2. **Anthropic: 长时应用开发的 Harness 设计模式**
   - 📝 来源：[Anthropic - Harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Mar 24, 2026)
   - 🎯 核心挑战：解决 Context Anxiety（上下文焦虑）和 Self-Evaluation Bias（自评偏差）
   - 💡 三 Agent 架构：Planner → Generator → Evaluator（借鉴 GAN 设计模式）
   - 🔑 创新组件：Initializer Agent + Coding Agent 双模式协作

3. **Termdock: AI Agent 工作流全栈指南**
   - 📝 来源：[Termdock - AI Agent Workflow Guide 2026](https://www.termdock.com/en/blog/ai-agent-workflow-guide-2026)
   - 🎯 核心价值：从聊天到终端自动化的完整工作流重构
   - 💡 实践要点：
     - Daily Standup 自动化报告生成
     - 多 Agent 并行代码审查系统
     - MCP 服务器集成最佳实践
     - 结构化 Prompt 工程方法论
   - 📚 适用场景：构建可重复、可版本控制的企业级 AI 系统

4. **热门 Agent Harness 框架动态（2026-03-28）**
   - [obra/superpowers](https://github.com/obra/superpowers) - **118,831** ⭐ (+3,151) 🚀 - Agentic Skills 框架
     - 📈 最新更新：技能插件自动注册系统、Codex 工具集成增强
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - **50,324** ⭐ (+2,023) 🚀 - 字节跳动 SuperAgent Harness
     - 📈 最新更新：better-auth 服务助手、CI 工作流优化、跨语言文档同步
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - **42,604** ⭐ (+172) - AI 驱动开发方法论
     - 📈 最新更新：插件市场重构、.claude-plugin 元数据支持
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - **47,380** ⭐ (+78) - 多 Agent 协作框架
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - **27,702** ⭐ (+115) - 图结构 Agent 构建
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - **20,361** ⭐ (+21) - OpenAI 官方 Agent SDK
   - [google/adk-python](https://github.com/google/adk-python) - **18,628** ⭐ (+19) - Google Agent 开发工具包

5. **Context Engineering 最佳实践新进展**
   - 🎯 核心原则："Give Codex a map, not a 1,000-page instruction manual"
   - 📋 文档策略：AGENTS.md 作为目录，docs/ 目录存放结构化知识
   - 🔧 机械强制：CI 任务验证文档新鲜度和交叉链接完整性
   - 💡 实践模式：CLAUDE.md + AGENTS.md 分层架构设计

**关键洞察**：
- 🚀 **生态爆发期**：Superpowers 突破 118K stars，DeerFlow 接近 50K stars，生态增长显著
- 🔧 **基础设施重要性**：配置一致性对 AI Agent 性能评估的关键影响被量化确认
- 🏗️ **架构演进**：三 Agent 架构（Planner-Generator-Evaluator）成为长时任务标准模式
- 🔌 **标准化进程**：MCP 集成、插件系统、元数据规范推动生态标准化
- 📊 **工程化成熟**：从概念验证转向生产就绪的工程化解决方案

---

### 2026-03-26 - 长时任务 Harness 设计 & 基准测试基础设施噪声

**重大更新**：

1. **Anthropic 长时任务 Harness 新方案**
   - 📝 来源：[Anthropic - Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
   - 🔑 核心创新：Initializer Agent + Coding Agent 双模式
   - 💡 关键组件：
     - `init.sh` 脚本初始化环境
     - `claude-progress.txt` 跨会话进度日志
     - 初始 git commit 作为基线

2. **Anthropic 基准测试基础设施噪声研究**
   - 📝 来源：[Anthropic - Quantifying Infrastructure Noise](https://www.anthropic.com/engineering/infrastructure-noise)
   - ⚠️ 发现：基础设施配置可使基准测试波动几个百分点
   - 💡 建议：
     - 指定资源的 guaranteed allocation 和 hard kill threshold
     - 3x ceiling 可将错误率从 5.8% 降到 2.1%
     - 排行榜差距 < 3% 需要质疑配置一致性

3. **Phil Schmid: Agent Harness 的苦涩教训**
   - 📝 来源：[Phil Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)
   - 🎯 核心洞察：Harness 将模糊的多步骤工作流转化为可记录、可评分的结构化数据
   - 💡 价值：允许有效地"爬山"优化

4. **Termdock 最新实践**
   - 📝 来源：[Termdock Blog](https://termdock.com/zh/blog)
   - 🎯 主题：SKILL.md vs CLAUDE.md vs AGENTS.md 配置最佳实践
   - 📚 最新文章：
     - 让 AI Agent 变笨的 10 个 CLAUDE.md 错误
     - 用 3 个 AI CLI 工具同时建构全端应用程式
     - AI CLI 品牌资产生成器

5. **热门 Agent Harness 框架更新（2026-03-26）**
   - [obra/superpowers](https://github.com/obra/superpowers) - 112,651 ⭐ (+614) - Agentic Skills 框架
   - [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) - 108,007 ⭐ - Agent Harness 性能优化系统
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - 47,212 ⭐ (+121) - 多 Agent 协作框架
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - 46,171 ⭐ (+171) - 字节跳动 SuperAgent Harness
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - 42,261 ⭐ (+261) - AI 驱动开发方法论
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - 27,475 ⭐ (+475) - 图结构 Agent 构建
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - 20,310 ⭐ (+310) - OpenAI 官方 Agent SDK
   - [google/adk-python](https://github.com/google/adk-python) - 18,594 ⭐ (+594) - Google Agent 开发工具包

---

### 2026-03-26 (早) - Agent-First 开发范式 & GAN 启发的多 Agent 架构

**重大更新**：

1. **OpenAI Harness Engineering 实践报告**
   - 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
   - 🔑 核心发现：团队用 3 名工程师 + Codex 在 5 个月内构建了百万行代码产品
   - 💡 关键原则："Humans steer. Agents execute." - 人类掌舵，代理执行
   - 📊 成果：平均每天 3.5 PR/工程师，零手写代码

2. **Anthropic 三 Agent 架构**
   - 📝 来源：[Anthropic Blog - Harness Design for Long-Running Apps](https://www.anthropic.com/engineering/harness-design-long-running-apps)
   - 🎯 新架构：Planner → Generator → Evaluator（规划器-生成器-评估器）
   - 💡 灵感：借鉴 GAN（生成对抗网络）设计多 Agent 系统
   - ⚠️ 关键问题解决：
     - Context Anxiety（上下文焦虑）：模型在接近上下文限制时过早结束
     - Self-Evaluation Bias（自评偏差）：Agent 对自己工作评价过高

3. **Context Engineering 黄金法则**
   - 📜 规则："Give Codex a map, not a 1,000-page instruction manual"
   - ✅ AGENTS.md 应作为目录，而非百科全书
   - ✅ 使用 docs/ 目录存放结构化知识库
   - ✅ 机械强制：CI 任务验证文档新鲜度和交叉链接

4. **热门 Agent Harness 框架更新**
   - [obra/superpowers](https://github.com/obra/superpowers) - 112K ⭐ - Agentic Skills 框架
   - [bytedance/deer-flow](https://github.com/bytedance/deer-flow) - 45K ⭐ - 字节跳动 SuperAgent Harness
   - [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) - 47K ⭐ - 多 Agent 协作框架
   - [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) - 42K ⭐ - AI 驱动开发方法论
   - [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) - 27K ⭐ - 图结构 Agent 构建
   - [openai/openai-agents-python](https://github.com/openai/openai-agents-python) - 20K ⭐ - OpenAI 官方 Agent SDK
   - [google/adk-python](https://github.com/google/adk-python) - 18K ⭐ - Google Agent 开发工具包

5. **Termdock AI CLI 最佳实践**
   - 📝 来源：[Termdock Blog](https://www.termdock.com/en/blog)
   - 🎯 主题：AI Agent 工作流、Claude Code、MCP、Agent Skills
   - 📚 最新文章：
     - AI Agent Workflow Guide 2026
     - Personal Recommendation Engine with AI CLI
     - Annual Review with AI CLI
     - Interview Practice with AI CLI

---

### 2026-03-25 - 初始化

**新增**：
- 创建项目结构
- 定义核心概念
- 规划文档框架

**参考来源**：
- [Neil Kakkar - How I'm Productive with Claude Code](https://neilkakkar.com/productive-with-claude-code.html)
- Claude Code Desktop 文档
- HEARTBEAT.md 中的 harness_engineering_daily 任务定义

---

*本指南持续更新中，每天汇总最新的 Harness Engineering 最佳实践和工具动态。*

