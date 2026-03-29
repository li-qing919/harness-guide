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

### 2026-03-29 - OpenAI 官方方法论发布 & Context Engineering 标准化

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

