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

