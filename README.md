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
