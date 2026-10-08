# 案例研究

## 案例 1：Tano 公司（Neil Kakkar）

**背景**：
- 6 周内 commit 数量显著增加
- 从"实现者"转变为"代理管理者"
- 基础设施建设 > 功能开发

**实施内容**：

| 阶段 | 解决的问题 | 工具/方法 |
|------|-----------|----------|
| 1 | PR 流程繁琐 | `/git-pr` skill |
| 2 | 编译等待时间长 | SWC |
| 3 | UI 验证成为瓶颈 | Preview 功能 |
| 4 | 只能串行开发 | Worktree + 端口隔离 |

**成果**：
- PR 描述质量提升
- 重启时间从 1 分钟降到 < 1 秒
- Agent 自己验证 UI
- 同时运行 5 个并行任务

**关键洞察**：
> "The highest-leverage work I've done at Tano hasn't been writing features. It's been building the infrastructure that turned a trickle of commits into a flood."

---

## 案例 2：OpenClaw 项目自身

**背景**：
- 开源 AI Agent 平台
- 需要支持多种模型和提供商
- 社区贡献活跃

**实施内容**：

### 1. 子代理系统

```yaml
# 配置示例
sessions_spawn:
  runtime: subagent
  mode: session
  maxConcurrent: 5
```

**使用场景**：
- 并行处理多个 GitHub issues
- 同时审查多个 PR
- 批量代码重构

### 2. 记忆系统

```yaml
memory:
  provider: lancedb
  embedding:
    provider: zhipu
    model: embedding-3
```

**使用场景**：
- 记住用户偏好
- 保存项目配置
- 跨会话知识积累

### 3. 定时任务

```bash
# 每日 PR 日报
openclaw cron add \
  --name "openclaw_daily_pr_report" \
  --cron "10 8 * * *" \
  --session isolated \
  --message "生成 OpenClaw PR 日报"

# Harness Engineering 日报
openclaw cron add \
  --name "harness_engineering_daily" \
  --cron "35 8 * * *" \
  --session isolated \
  --message "搜集 Harness Engineering 动态"
```

**成果**：
- 自动化日报生成
- 实时监控 MCP 支持进展
- 社区动态追踪

---

## 案例 3：Vue + SpringBoot 全栈项目

**背景**：
- 前端：Vue 3 + TypeScript
- 后端：Spring Boot + Java
- 需要前后端并行开发

**实施内容**：

### 1. Worktree 配置

```bash
# 前端 worktree
git worktree add ../frontend-feat1 -b feat/frontend-1

# 后端 worktree
git worktree add ../backend-feat1 -b feat/backend-1
```

### 2. 端口隔离

```bash
# frontend-feat1/.env
FRONTEND_PORT=3001
BACKEND_PORT=8001

# backend-feat1/.env
FRONTEND_PORT=3002
BACKEND_PORT=8002
```

### 3. Agent 工作流

```
1. PM Agent → PRD
2. Architect → API 设计
3. Backend Engineer → 实现 API + 测试
4. Frontend Engineer → 实现界面 + 测试
5. QA Agent → E2E 测试
6. Code Reviewer → 审查并合并
```

**成果**：
- 前后端独立开发
- 自动化测试覆盖
- 快速迭代

---

## 案例 4：Claude Code Desktop 用户

**背景**：
- 个人开发者
- 主要做前端开发
- 需要 UI 验证

**实施内容**：

### 1. launch.json 配置

```json
{
  "version": "0.0.1",
  "configurations": [
    {
      "name": "frontend",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000,
      "autoPort": true
    }
  ],
  "autoVerify": true
}
```

### 2. CLAUDE.md 规则

```markdown
## UI 验证规则

1. 任何前端代码变更，必须：
   - 启动 dev server
   - 使用 Preview 访问页面
   - 截图确认 UI
   - 测试交互

2. 禁止声明"完成"而未经 UI 验证

3. 发现问题时自动修复
```

**成果**：
- 无需手动验证 UI
- Claude 自己发现并修复错误
- 专注于架构设计

---

## 案例 5：多 Agent 协作项目

**背景**：
- 大型项目
- 多个团队协作
- 需要严格的流程控制

**实施内容**：

### 1. Agent 角色分工

| Agent | 职责 | 触发条件 |
|-------|------|---------|
| PM Agent | 需求分析 | 新需求 |
| Architect | 技术设计 | PRD 完成 |
| Engineer | 代码实现 | 设计完成 |
| QA Agent | 测试验证 | 代码完成 |
| Reviewer | 代码审查 | 测试通过 |

### 2. 工作流编排

```yaml
workflow:
  stages:
    - name: planning
      agents: [PM, Architect]
      output: [PRD, Design]
    
    - name: implementation
      agents: [Engineer]
      parallel: 5
      output: [Code, Tests]
    
    - name: verification
      agents: [QA]
      output: [TestReport]
    
    - name: review
      agents: [Reviewer]
      output: [Approval]
```

### 3. 质量门禁

```yaml
quality_gates:
  - type: code_coverage
    threshold: 80%
  
  - type: security_scan
    level: high
  
  - type: performance_test
    threshold: 200ms
  
  - type: human_approval
    required: true
```

**成果**：
- 清晰的职责分工
- 自动化质量检查
- 可追溯的开发流程

---

## 总结：成功模式

### 共同特征

1. **基础设施优先**
   - 先搭建 Harness 层
   - 再开发功能
   - 持续优化流程

2. **自动化验证**
   - UI 自动验证
   - 测试自动运行
   - 错误自动修复

3. **并行开发**
   - Worktree 隔离
   - 端口隔离
   - 独立环境

4. **持续学习**
   - 每日阅读日报
   - 更新配置
   - 优化流程

### 常见陷阱

1. **过早优化**
   - 先跑起来
   - 再优化
   - 不要追求完美

2. **忽视验证**
   - UI 验证很重要
   - 自动化测试必不可少
   - 人工审查不能省

3. **过度并行**
   - 从 2 个开始
   - 逐步增加
   - 监控资源

---

*更新时间：2026-03-30*

---

## 2026-03-28 补充：行业理论与实战新洞察

### Deloitte 2026 技术预测要点
- 德勤明确将 **AI Agent 编排** 列为关键解锁
- 工作流模块化 + Agent 驱动 = 指数级价值释放
- 2026 年 AI agent 将横跨多种编程语言、框架、基础设施和通信协议扩散

### Harness Engineering 理论体系成型
- **核心定义（Agent Engineering）**：设计约束、工具、反馈循环、文档和验证系统的学科
- **Epsilla 核心论点**："Agents aren't hard; the Harness is hard." — 约束解空间反而提升可靠性
- **历史脉络**：2025 Mitchell Hashimoto 提出 → 2026 OpenAI 内部实验正式化
- **解决痛点**：纯上下文工程在长时任务中仍会漂移、积累"熵"

### 多 Agent 框架选型参考（2026 最新）
| 框架 | 核心优势 | 最佳场景 |
|------|---------|---------|
| OpenAI Agents SDK | 轻量、快速迭代 | OpenAI 生态项目 |
| LangGraph | 图结构工作流 | 复杂状态机 |
| CrewAI | 角色扮演协作 | 模拟团队协作 |
| AutoGen/AG2 | 对话式编排 | 多轮对话 |
| Google ADK | 代码优先、双版本 | Google Cloud 集成 |
| Claude Agent SDK | Anthropic 原生 | Claude 模型场景 |

### 安全修复趋势（2026-03-28）
- **CrewAI**: defusedxml 替换 xml.etree.ElementTree 防 XXE 攻击
- **LangGraph**: cryptography 升级 + 威胁模型文档
- **Google ADK**: MCP 认证缺失问题暴露
- **OpenAI Agents SDK**: 流式 guardrail 修复
- 📌 **启示**：Agent 框架安全成熟度快速提升，企业采用前应关注安全更新频率

---

## 2026-03-29 补充：Harness Engineering 主流化 & Context Engineering 标准化

### Harness Engineering 获得主流媒体关注
- **朝鲜日报（Chosun）** 英文版报道：Harness Engineering 成为 AI 编码时代人类开发者新角色
- **NXCode 完整指南**：详细阐述 Harness Engineering 包含但不限于 Context Engineering 和 Prompt Engineering
- **核心要点**：Harness Engineering 运作在更高层面——关注让 Agent 可靠的完整系统：约束条件、反馈循环、文档化和生命周期管理

### Superpowers v5.0.6 里程碑
- **Inline Self-Review 替代 Subagent Review Loops**
- 执行时间减少约 25 分钟而不降低质量
- 📌 **启示**：Review 机制正在从"多 Agent 评审"转向"单 Agent 自审"，减少编排开销

### DeerFlow 51K+ stars 里程碑
- 字节跳动 DeerFlow 持续增长突破 51K
- lead_agent 支持自定义 Channel Assistant ID
- 修复 MemoryMiddleware 和 task_tool 中 thread_id 回调逻辑
- 📌 **启示**：长周期 SuperAgent 框架在生产环境中的记忆管理仍是关键挑战

---

## 2026-03-30 补充：Harness Engineering 行业转向 & 框架重大迭代

### 行业焦点从 Agent 转向 Harness
- **Aakash Gupta (Medium 热文)**：["2025 Was Agents. 2026 Is Agent Harnesses."](https://aakashgupta.medium.com/2025-was-agents-2026-is-agent-harnesses-heres-why-that-changes-everything-073e9877655e)
- 核心论点：Agent 本身是引擎，Harness 是整车——决定了 Agent 能否在生产环境中稳定交付
- 📌 **启示**：行业共识加速形成，Harness Engineering 对产品管理和技术架构的影响日益显著

### DeerFlow 2.0 全面重写
- 字节跳动 DeerFlow 2.0 已完成全面重写，与 v1 无代码共享
- 定位从 Agent 框架升级为 **"Super Agent Harness"**
- 2026-02-28 登顶 GitHub Trending 第一名
- 修复了 artifacts API 存储型 XSS 漏洞（CVE）
- 📌 **启示**：Super Agent Harness 正在从实验性框架走向生产级平台

### Superpowers 进入 Claude 官方市场
- 122,872 ⭐，目前 star 数最高的 Agent 技能框架
- 真正的红/绿 TDD 流程：Agent 理解需求 → 生成规格 → 用户确认 → 实施计划 → 子 Agent 开发
- 📌 **启示**：AI 编程助手市场正在形成标准化技能分发渠道

### LangGraph 获评 2026 最佳框架
- 多家独立评测将 LangGraph 评为 2026 年最佳 AI Agent 框架
- 核心优势：持久化执行（Durable Execution）+ 综合记忆系统
- 📌 **启示**：生产级部署能力成为框架竞争的关键差异点

---

## 2026-03-31 补充：Martin Fowler 深度点评 & 框架生态持续增长

### Martin Fowler 对 Harness Engineering 的深入点评
- 📝 来源：[Martin Fowler - Exploring Gen AI: Harness Engineering](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)
- 🎯 探讨了自定义 lint 规则、结构性测试、基础上下文和知识文档等是否将成为新一代服务模板
- 💡 提出代码库设计模式的"Harness 化"是否会成为新的抽象层
- 📌 **启示**：Martin Fowler 的持续关注表明 Harness Engineering 正在从前沿实践走向软件工程主流方法论

### Phil Schmid：Context Engineering 是新技能，不是 Prompting
- 📝 来源：[Phil Schmid - Context Engineering](https://www.philschmid.de/context-engineering)
- 🎯 核心论点：Context Engineering 是设计和构建动态系统的学科——在正确的时间、以正确的格式、为 LLM 提供正确信息和工具
- 💡 Context 不只是单一 prompt，而包括对话历史、工具定义、检索文档、用户画像和系统指令的完整数据环境
- 📌 **启示**：Context Engineering 正在从"高级 Prompting 技巧"重新定义为独立的系统工程学科

### 框架生态持续增长（2026-03-31 数据）
- [obra/superpowers](https://github.com/obra/superpowers) — **125,616** ⭐ 🚀（+2,744）— 突破 125K
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **54,031** ⭐ 🚀（+1,539）— 突破 54K
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,617** ⭐（+107）
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **42,981** ⭐（+158）
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **27,963** ⭐（+94）
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,426** ⭐（+24）
- [google/adk-python](https://github.com/google/adk-python) — **18,672** ⭐（+18）— ADK 2.0 Alpha 重大更新

### Google ADK 2.0 Alpha 亮点
- 🔥 引入**确定性工作流和图式编排**
- 新增 **LocalEnvironment** 用于本地命令执行和文件 I/O
- 新增 **EnvironmentToolset** 支持
- 支持 **A2A 协议**和 **MCP 集成**
- 📌 **启示**：Google ADK 正在从轻量 SDK 演进为完整的 Agent 编排平台

---

## 2026-04-01 补充：LangChain Harness 实战 & Phil Schmid 定义 Agent Harness

### LangChain DeepAgents Harness Engineering 实战：TerminalBench 提升 13.7 分
- 📝 来源：[LangChain Blog - Improving Deep Agents with Harness Engineering](https://blog.langchain.com/improving-deep-agents-with-harness-engineering/)
- 🎯 通过优化 System Prompt、Tools 和 Middleware 三个维度，TerminalBench 2.0 分数从 **52.8 → 66.5**
- 💡 引入 **Ralph Wiggum Loop**（验证循环）和 **Multi-model Harness**（多模型脚手架）概念
- 📌 **启示**：这是 Harness Engineering 落地的标杆案例——不换模型，仅通过优化脚手架就能显著提升 Agent 表现

### Phil Schmid：Agent Harness 是 2026 年最重要的基础设施
- 📝 来源：[Phil Schmid - The Importance of Agent Harness in 2026](https://www.philschmid.de/agent-harness-2026)
- 🎯 核心论点：Agent Harness 不是 Agent 本身，而是围绕模型管理长周期任务的**基础设施层**
- 💡 Harness 包含：prompt 预设、工具调用编排、生命周期钩子和子 Agent 管理
- 🔑 **关键金句**："好模型配差 Harness 不如差模型配好 Harness"
- 📌 **启示**：Harness 是 Agent 的操作系统——这进一步确认了 Harness Engineering 作为独立学科的地位

### Superpowers v5.0.7 扩展至 GitHub Copilot CLI
- 🆕 新增 GitHub Copilot CLI 支持：SessionStart 上下文注入、工具映射表
- 128,140 ⭐，保持最快增长速度
- 📌 **启示**："跨编码 Agent 通用技能"定位进一步强化——同一套技能可同时用于 Claude Code、Cursor、Codex 和 GitHub Copilot CLI

### 框架生态持续增长（2026-04-01 数据）
- [obra/superpowers](https://github.com/obra/superpowers) — **128,140** ⭐ 🚀（+2,524）— 新增 Copilot CLI 支持
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **55,237** ⭐ 🚀（+1,206）— Sandbox 稳定性修复
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,726** ⭐（+109）— v1.13.0a4 alpha
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,099** ⭐（+118）— 技能设计优化
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,068** ⭐（+105）— v1.1.4 发布
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,463** ⭐（+37）— v0.13.3
- [google/adk-python](https://github.com/google/adk-python) — **18,687** ⭐（+15）— 安全加固

---

## 2026-04-02 补充：Anthropic Harness 迭代经验 & Context Engineering 新范式

### Anthropic 发布 Harness 设计迭代经验
- 📝 来源：[Anthropic - Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- 🎯 从初始化 Agent + 编码 Agent 的**双 Agent 架构简化为更统一的结构**
- 💡 加入针对 AI 功能构建的 prompt 优化
- 🔑 揭示了 Harness 设计如何**突破基线模型的性能天花板**
- 📌 **启示**：Harness 架构本身需要持续迭代——不是一次设计定终身，而是像软件一样持续重构

### NxCode 发布 Harness Engineering 完整指南
- 📝 来源：[NxCode - Harness Engineering Complete Guide 2026](https://www.nxcode.io/resources/news/harness-engineering-complete-guide-ai-agent-codex-2026)
- 🎯 将 Harness Engineering 定义为"设计让 AI Agent 可靠运行的系统"的**新学科**
- 💡 覆盖四大核心：约束机制、反馈循环、文档化和生命周期管理
- 📌 **启示**：2026 年 Harness Engineering 概念普及的重要推手，提供了面向开发者的系统化入门

### LangChain Context Engineering 四大策略框架
- 📝 来源：[LangChain Blog - Context Engineering for Agents](https://blog.langchain.com/context-engineering-for-agents/)
- 🎯 系统化归纳为 **Write / Select / Compress / Isolate** 四大策略桶
- 💡 结合 LangGraph 长期记忆能力，提供跨会话持久化上下文的生产级方案
- 引用多个主流 Agent 产品的实际实现案例验证策略通用性
- 📌 **启示**：Context Engineering 的策略框架正在走向标准化，四大策略桶成为共识

### Context Engineering 2026 三大认知颠覆
- 📝 来源：[The AI Corner - Context Engineering Guide 2026](https://www.the-ai-corner.com/p/context-engineering-guide-2026)
- ⚡ **"Think step by step" 对推理模型有害** — 干扰原生推理链
- ⚡ **长 prompt 超 3000 token 开始降性能** — 注意力分散存在拐点
- ⚡ **Few-shot CoT 仅剩格式对齐** — 推理模型不需要示例教"怎么想"
- 💡 替代方案：**Prompt-as-Code** 配合版本控制 + 6 组件 prompt 架构 + 4 层防御策略
- 📌 **启示**：2026 年 Context Engineering 的认知框架全面升级，很多 2025 年的"最佳实践"已经过时

### 框架生态持续增长（2026-04-02 数据）
- [obra/superpowers](https://github.com/obra/superpowers) — **130,230** ⭐ 🚀（+2,090）— 突破 130K 里程碑 🎉
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **56,018** ⭐ 🚀（+781）— 网关层稳定性修复
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,789** ⭐（+63）— A2UI 扩展 + GPT-5 支持
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,229** ⭐（+130）— checkpoint-preview skill
- [langchain-ai/langgraph](https://langchain-ai/langgraph) — **28,160** ⭐（+92）— configurable metadata
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,494** ⭐（+31）— v0.13.4 approval policies
- [google/adk-python](https://github.com/google/adk-python) — **18,694** ⭐（+7）— gemini-3.1-flash-live-preview

---

## 2026-04-03 补充：Phil Schmid Context Engineering Part 2 & 框架新里程碑

### Phil Schmid Context Engineering Part 2：Context Rot 深度解决方案
- 📝 来源：[Phil Schmid - Context Engineering Part 2](https://www.philschmid.de/context-engineering-part-2)
- 🎯 深入探讨 **Context Rot（上下文腐烂）** 问题及解决方案
- 💡 Manus 的 Peak Ji 分享 Agent Harness 演进经验：
  - **Context Compaction 可逆化**：压缩后可通过工具读回文件
  - **子代理上下文隔离**：避免 KV-cache 膨胀和无关信息干扰
  - **分层路由策略**：简单任务用快模型，复杂任务用强模型
- 📌 **启示**：上下文腐烂是长时任务 Agent 的核心挑战，可逆压缩和隔离是前沿解决方案

### Anthropic Effective Context Engineering 实践补充
- 📝 来源：[Anthropic - Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- 🎯 Context Engineering 不仅是 Prompt Engineering，而是管理系统指令、工具定义、MCP、外部数据、消息历史的**全部上下文状态**
- 💡 **Tool Result Clearing** 是最轻量的上下文压缩手段
- 💡 **基于文件的 Memory 系统** 通过将信息存储在上下文窗口之外解决长对话膨胀
- 💡 子代理应只接收与其任务相关的上下文，而非共享全局完整历史

### State of Context Engineering 2026：60% 利用率阈值
- 📝 来源：[State of Context Engineering in 2026 (Medium)](https://medium.com/@kushalbanda/state-of-context-engineering-in-2026-cf92d010eab1)
- 🎯 提出明确的 **60% 上下文利用率阈值**管理规则
- 💡 生产系统应分层叠加：Progressive Disclosure + Tool Management → Routing + Compression → Retrieval
- 📌 **启示**：可量化的阈值管理使 Context Engineering 从定性建议走向定量工程

### 框架生态持续增长（2026-04-03 数据）
- [obra/superpowers](https://github.com/obra/superpowers) — **132,186** ⭐ 🚀（+1,956）— 突破 132K
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **56,695** ⭐ 🚀（+677）— 文档站点上线
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,865** ⭐（+76）— **v1.13.0 正式发布** 🎉
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,365** ⭐（+136）— CI 质量检查
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,265** ⭐（+105）— deploy revisions list
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,521** ⭐（+27）— v0.13.4
- [google/adk-python](https://github.com/google/adk-python) — **18,714** ⭐（+20）— gemini-3.1-flash-live-preview

---

## 2026-04-04 补充：行业标准化里程碑 & 上下文瓶颈新认知

### Anthropic 发布《2026 Agentic Coding Trends Report》
- 📝 来源：[Anthropic - 2026 Agentic Coding Trends Report](https://resources.anthropic.com/hubfs/2026%20Agentic%20Coding%20Trends%20Report.pdf)
- 🎯 八大趋势涵盖：
  1. **单 Agent 演化为协调团队** — 从单体 Agent 到多 Agent 协作系统
  2. **长时运行 Agent 构建完整系统** — Agent 能力从片段编码扩展到端到端交付
  3. **Agentic QA（AI 审查 AI 代码）成为标准** — 质量控制闭环
  4. **双用途风险需要安全优先架构** — 安全成为设计约束而非事后检查
- 💡 核心结论：2025 年 coding agent 已从实验工具转为生产系统，2026 年是工程化和安全化的一年
- 📌 **启示**：Agentic QA（AI 审查 AI）的标准化标志着 Harness Engineering 中 Evaluator Agent 模式被广泛接受

### Linux Foundation 成立 Agentic AI Foundation (AAIF)
- 📝 来源：[Linux Foundation - Agentic AI Foundation](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
- 🔥 Anthropic 的 **MCP**、Block 的 **goose**、OpenAI 的 **AGENTS.md** 作为创始项目入驻
- 🎯 建立中立组织推动开源 Agentic AI 技术标准化
- 💡 MCP 被比喻为"**AI 的 USB-C 接口**"——统一 Agent 与工具的连接标准
- 📌 **启示**：Agent 技术标准化进入新阶段，MCP/AGENTS.md 获得行业基金会级别支持

### OpenAI 发布 Harness Engineering 深度实践博客（Ryan Lopopolo）
- 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
- 🎯 详述团队以"**零手写代码**"为约束，为 Codex 构建完整 Harness 的实践
- 💡 核心组件：自定义 linter、结构测试、上下文文档、**Agent Reviewer 循环（Ralph Wiggum Loop）**
- 🔑 核心理念："每次发现 Agent 犯错，就工程化一个方案让它永远不再犯"
- 📌 **启示**：OpenAI 内部 Harness 实践的公开化，Ralph Wiggum Loop 的详细描述为社区提供了可参考的验证循环模式

### Epsilla：AI 工程三阶段演进论
- 📝 来源：[Epsilla - Harness Engineering Evolution](https://www.epsilla.com/blogs/harness-engineering-evolution-prompt-context-autonomous-agents)
- 🎯 AI 工程三个演进阶段：**Prompting → Context Engineering → Harness Engineering**
- 💡 核心论点："**Agent 不难，Harness 难**" — 通过规则、反馈循环和 linter 约束解空间
- 💡 结合**语义图谱**实现 Agent-as-a-Service
- 📌 **启示**：三阶段模型为理解 AI 工程演进提供了清晰的历史框架

### Context is AI Coding's Real Bottleneck in 2026
- 📝 来源：[The New Stack - Context is AI Coding's Real Bottleneck](https://thenewstack.io/context-is-ai-codings-real-bottleneck-in-2026/)
- 🎯 AI 编码的真正瓶颈不是模型能力，而是**上下文** — 工程师脑中知识 vs AI 能理解信息的鸿沟
- 💡 阅读 AI 生成代码需要不同的认知工作：**从输出逆向工程意图**，而非跟随同事的推理过程
- 💡 成功的团队将形成"**人类做判断和创造性工作，AI 处理重复任务**"的节奏
- 📌 **启示**：上下文瓶颈不仅是技术问题，更是认知和工作流设计问题

### 框架生态持续增长（2026-04-04 数据）
- [obra/superpowers](https://github.com/obra/superpowers) — **134,026** ⭐ 🚀（+1,840）— 突破 134K
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **57,301** ⭐ 🚀（+606）— 文档上下文注入
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **47,957** ⭐（+92）— AMP Training Tab
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,485** ⭐（+120）— context-aware planning
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,356** ⭐（+91）— v1.1.6
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,551** ⭐（+30）— vLLM 兼容
- [google/adk-python](https://github.com/google/adk-python) — **18,730** ⭐（+16）— Easy GCP

---

## 2026-04-05 补充：MCP+A2A 双协议时代 & Mem0 记忆驱动 Context Engineering

### 行业动态：MCP+A2A 双协议成为互操作性标准

2026 年 4 月，主要 Agent 框架相继支持 **MCP（Model Context Protocol）** 和 **A2A（Agent-to-Agent Protocol）** 双协议：

| 框架 | MCP 支持 | A2A 支持 | 传输机制 |
|------|---------|---------|---------|
| LangGraph | ✅ | ✅（通过 LangSmith） | 标准 MCP |
| CrewAI | ✅ | ✅（2026-03 新增） | Stdio、SSE、Streamable HTTPS |
| Google ADK | ✅ | ✅ | 原生集成 |
| OpenAI Agents SDK | ✅ | 部分 | OpenAI 生态 |

- 📌 **启示**：MCP+A2A 双协议成为 2026 Agent 互操作性的事实标准，框架间协作能力大幅提升

### Mem0：记忆驱动的 Context Engineering 完整指南

- 📝 来源：[Mem0 - Context Engineering for AI Agents: Complete Guide](https://mem0.ai/blog/context-engineering-ai-agents-guide)
- 🎯 将 Context Engineering 定义为"**结构化上下文和记忆使 AI 系统随时间智能行为的系统方法**"
- 💡 核心支柱：记忆管理（持久化+语义检索）、RAG 增强（检索+压缩结合）、智能格式化（精确时机精确信息）
- 🏗️ 记忆压缩引擎：写入持久存储 → 语义搜索 → 智能压缩 → 记忆类型隔离
- 📌 **启示**：与 Phil Schmid 的信息流架构设计形成互补，从记忆持久化和检索角度完善 Context Engineering 实践

### 框架生态持续增长（2026-04-05 数据）

- [obra/superpowers](https://github.com/obra/superpowers) — **135,171** ⭐ 🚀（+1,145）— 社区生态建设
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **48,034** ⭐（+77）— 突破 48K，MCP+A2A 双协议
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **57,803** ⭐（+502）— 边缘场景健壮性
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,565** ⭐（+80）— 贡献规范化
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,419** ⭐（+63）— MCP+A2A 双协议
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,570** ⭐（+19）— flush_traces API
- [google/adk-python](https://github.com/google/adk-python) — **18,741** ⭐（+11）— 凭证自动刷新

---

## 2026-04-08 补充：Anthropic 三 Agent 架构深度解析 & HumanLayer 四杠杆模型

### Anthropic：长时运行应用开发的 Harness 设计深度解析

- 📝 来源：[Anthropic - Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps)
- 🎯 Anthropic 工程团队正式公开其**三 Agent Harness** 架构：初始化器 + 编码器 + 上下文交接
- 💡 设计源自 Claude Code 的前端设计技能和早期长时运行编码 Agent 实验
- 🔑 核心机制：
  1. **初始化器**：分解产品规格为任务列表
  2. **编码器**：逐功能实现，每完成一个功能后重置上下文
  3. **上下文交接**：跨会话传递进度、决策和文件状态
- 💡 **Opus 4.6** 在此架构基础上显著提升了规划能力和长上下文检索
- 📌 **启示**：三 Agent 架构解决了单 Agent 长时运行的上下文退化问题，通过上下文重置和制品交接维持质量

### OpenAI：Harness Engineering 方法论官方详解

- 📝 来源：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)
- 🎯 OpenAI 详细阐述了内部 Harness Engineering 方法论，利用 Codex AI Agent 执行代码编写、测试生成和可观测性管理
- 🔑 核心思想：将脚手架、反馈循环、文档和架构约束编码为**机器可读制品**，让 Agent 在工程工作流中可靠执行
- 💡 关键实践：
  1. 从空 Git 仓库起步，以仓库知识为记录系统
  2. 追求 Agent 可读性（Agent Legibility）
  3. 执行计划（Execution Plans）驱动任务分解
- 📌 **启示**：机器可读制品是 Harness Engineering 的核心输出，将人类知识转化为 Agent 可消费的格式

### HumanLayer：四杠杆模型 —— Harness 作为 Context Engineering 子集

- 📝 来源：[HumanLayer - Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)
- 🎯 HumanLayer CTO 将 Harness Engineering 精确定义为 Context Engineering 的子集
- 💡 四个核心杠杆：系统提示、工具/MCP 选择、子 Agent、钩子
- 🔑 深度分析了 Claude Code 的 Explore/Bash 子 Agent 如何实现上下文封装
- 📌 **启示**：提供了一个简洁的理论框架——Harness Engineering 就是通过四个杠杆精确控制 Agent 的上下文窗口

### Sud Shekhar：从 Prompts 到 Context 的自主 Agent 演进

- 📝 来源：[Sud Shekhar - Mastering Context Engineering for Autonomous AI Agents](https://www.sudshekhar.com/blog/from-prompts-to-context-mastering-context-engineering-for-autonomous-ai-agents)
- 🎯 2026 年可靠 AI 的核心是 Context Engineering——为自主 Agent 架构动态信息环境
- 💡 从静态提示到动态上下文到完整 Harness 的三阶段演进
- 🔑 自主 Agent 的关键要素：结构化上下文注入、记忆管理、状态追踪、错误恢复
- 📌 **启示**：Context Engineering 从定性建议走向结构化工程，Entry Control → Runtime Management → Knowledge Layer 三层架构

### 框架生态持续增长（2026-04-08 数据）

- [obra/superpowers](https://github.com/obra/superpowers) — **139,330** ⭐ 🚀（+4,159）— 社区运营规范化
- [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — **59,117** ⭐（+1,314）— 循环检测哈希优化
- [crewAIInc/crewAI](https://github.com/crewAIInc/crewAI) — **48,278** ⭐（+244）— 线程安全修复
- [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — **43,896** ⭐（+331）— installer 品牌化
- [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) — **28,647** ⭐（+228）— uv workspace 支持
- [openai/openai-agents-python](https://github.com/openai/openai-agents-python) — **20,633** ⭐（+63）— HoneyHive 追踪集成
- [google/adk-python](https://github.com/google/adk-python) — **18,797** ⭐（+56）— Trigger 端点

---

## 案例 10：旧金山 Harness Engineering & AI Factories 实地报告（2026-06-14 更新）

**来源**：[Escape.tech - Everything I Learned About Harness Engineering and AI Factories in SF](https://escape.tech/blog/everything-i-learned-about-harness-engineering-and-ai-factories-in-san-francisco-april-2026)

2026 年 3 月最后一周，作者在旧金山与各规模公司的 CTO/CPO/工程领袖交流 AI Agent 实战经验，参加 YC DevTool Day（3/27）和 All Things Dev（3/31），总结了当前行业最前沿的 Harness Engineering 实践模式。

### 核心发现

1. **AI 工厂架构分化**：
   - 大型企业倾向于自研 Harness 层 + 多模型路由
   - 中型公司偏好 LangGraph + LangSmith 组合
   - 初创公司多从 DeerFlow / CrewAI 起步

2. **Harness Engineering 已成为独立角色**：
   - 从 DevOps / Platform Engineering 中分化出来
   - 专注于 Agent 上下文管理、工具编排、评估闭环
   - 需要同时理解 ML 和传统软件工程

3. **生产化关键挑战**：
   - 评估闭环（Eval Loop）是最耗时但最关键的投资
   - 上下文文件维护（AGENTS.md 等）需要流程化保障
   - 多 Agent 协作的调试和可观测性仍是痛点

### 行业阶段判断

> Harness Engineering 正处于从 "前沿实践" 向 "行业标准" 过渡的拐点。2026 年下半年将看到更多标准化工具和最佳实践的出现。

---

## 案例 11：OpenAI Harness Engineering 官方指南（2026-06-18）

**来源**：[OpenAI - Harness Engineering](https://openai.com/index/harness-engineering)

OpenAI Codex 团队详细阐述了 Harness Engineering 方法论——在超百万行代码、零人工编写的生产应用中，关键不在模型本身，而在围绕模型构建的约束、反馈循环、文档、linter 和生命周期管理系统。

### 核心原则

1. **Agent Legibility（智能体可读性）**：所有项目文档、约定和架构决策必须以 Agent 可消费的格式编写
2. **Repository Knowledge as System of Record**：代码仓库本身是知识的唯一可信来源，而非外部 wiki 或文档
3. **约束即自由**：通过 linter、CI 和自动化检查为 Agent 构建安全护栏，使其能在更大范围内自主操作

### 启示

> Harness Engineering 的核心论点：「模型不是瓶颈，harness 才是」。同样的模型，不同的 harness，产出质量天差地别。

---

## 案例 12：Anthropic 长期运行 Agent 的有效 Harness 设计（2026-06-18）

**来源**：[Anthropic - Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

Anthropic 分享了 Claude Agent SDK 在跨上下文窗口运行时的 harness 设计方案。

### 编排架构

- **初始化 Agent（Initializer Agent）**：设置环境——运行 `init.sh` 脚本、创建 `progress.txt` 进度文件、执行初始 git commit
- **编码 Agent（Coding Agent）**：在增量推进中留下清晰工件（artifact）供下一轮使用
- **上下文交接**：通过文件系统和 git commit 实现 Agent 间的信息传递

### 关键设计决策

- 每轮 Agent 的输出必须是下一轮的可靠输入
- 使用文件系统作为持久化层，而非依赖 Agent 的「记忆」
- git commit 作为检查点（checkpoint），提供回滚能力

### 启示

这是生产级多窗口 Agent 编排的参考实现，解决了「长任务超出单上下文窗口」的核心挑战。

---

## 案例 13：LangChain Terminal Bench 2.0——Harness 优化实证（2026-06-18）

**来源**：[LangChain - Improving Deep Agents with Harness Engineering](https://www.langchain.com/blog/improving-deep-agents-with-harness-engineering)

### 实验

LangChain 团队在 Terminal Bench 2.0 上进行了一项关键实验：**不更换模型**（gpt-5.2-codex），仅通过优化 harness 来提升得分。

### Harness 优化措施

- **自我验证**：Agent 在提交前自动验证输出
- **Tracing 改进**：更精细的执行追踪，帮助定位失败点
- **工具调用优化**：减少冗余工具调用，优化工具结果处理

### 结果

| 指标 | 优化前 | 优化后 |
|------|--------|--------|
| 得分 | 52.8 | **66.5** |
| 排名 | Top 30 | **Top 5** |

### 启示

> 这是「harness 比模型更重要」论点的最强实证之一。相同的模型，仅通过 harness 优化，排名从 Top 30 跃升至 Top 5。

---

## 案例 14：Philipp Schmid Agent Harness 概念框架（2026-06-18）

**来源**：[Philipp Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)

Philipp Schmid（Google DeepMind）系统定义了 Agent Harness 的概念框架。

### 定义

> Agent Harness 是「包裹 AI 模型以管理长期任务的基础设施层」。

### 核心能力

- Prompt 预设与模板管理
- 工具调用编排
- 生命周期 hooks（启动、暂停、恢复、终止）
- 规划与子 Agent 管理
- 上下文窗口管理

### 层级定位

> Agent Harness 运行在比 Agent 框架更高的层级——Agent 框架关注单个 Agent 的能力，Agent Harness 关注如何让多个 Agent 协同完成长期任务。

Schmid 强调，Agent Harness Engineering 是 2026 年 Agent 工程的核心学科。

---

## 案例 15：arXiv 论文——自然语言 Agent Harnesses（NLAHs）（2026-06-18）

**来源**：[arXiv - Natural-Language Agent Harnesses](https://arxiv.org/html/2603.25723v1)

### 核心贡献

论文将 harness 控制逻辑外化为可移植的自然语言工件（NLAHs），配合 Intelligent Harness Runtime（IHR）执行。

### 设计模式

1. **显式契约（Explicit Contracts）**：Agent 行为通过明确的自然语言契约定义，而非隐含在代码中
2. **持久化工件（Persistent Artifacts）**：harness 配置作为可版本控制的工件持久化存储
3. **轻量适配器（Lightweight Adapters）**：harness 可通过适配器在不同运行时之间迁移

### 学术意义

推动 harness 工程从运行时特定约定走向可迁移、可比较的科学对象。这是首批将 Harness Engineering 作为正式研究对象进行系统分析的学术论文之一。

### 2026-09-12 补充：传播量化数据

- PY 频道深度解读视频（2026-04-14，15.7 万播放）同时覆盖本文与 Stanford《Meta-Harness: Automated Optimization of Agent Harnesses End-to-End》（自动优化 harness 的端到端方法，另见 01-architecture Meta-Harness 引注）
- 视频给出的量化对比：**同一模型同一 benchmark 下，harness 差异可造成 6 倍性能差距**——优化 harness 的回报高于等待下一代基础模型
- 与「伟大的均衡器」实证（#37 2026-09-07 补充：开源模型追平前沿模型）互为印证，均为「harness 投入产出比高于模型等待」提供数据支撑

---

## 案例 16：Anthropic 与 OpenAI 的 Agent 架构趋同（2026-07-05）

**来源**：[Medium - Anthropic and OpenAI Just Shipped the Same Answer](https://medium.com/@rajasekar-venkatesan/anthropic-and-openai-just-shipped-the-same-answer-to-ai-agents-seven-days-apart-c19f2dc03244)

### 背景

2026 年 4 月，Anthropic 发布 Managed Agents，OpenAI 发布 Agents SDK 的重大更新——两者在七天内独立发布了几乎相同的 Agent 架构。

### 共同架构

两家公司不约而同地采用了相同的架构要素：

```
┌─────────────────────────────────────────────┐
│              Control Plane                    │
│  • 任务编排和生命周期管理                       │
│  • 检查点和状态持久化                           │
│  • 凭据隔离和安全管理                           │
└──────────────────┬──────────────────────────┘
                   │
┌──────────────────┴──────────────────────────┐
│            Compute Plane                      │
│  • 沙箱执行环境                               │
│  • 工具调用和结果返回                           │
│  • 端到端追踪                                 │
└─────────────────────────────────────────────┘
```

### 行业意义

> 两家顶级 AI 公司在七天内独立发布几乎相同的架构，说明生产级 Agent 的需求已经形成了行业共识——问题空间的客观约束决定了架构选择。

### 启示

- Agent 架构正在从「百花齐放」走向「行业共识」
- 生产级需求（安全、隔离、追踪、持久化）是架构趋同的驱动力
- 框架选型风险降低：主流方案越来越相似，切换成本在下降

---

## 案例 17：四年 AI Agent 模式演进史（2026-07-05）

**来源**：[Bits-Bytes-NN - Evolution of AI Agentic Patterns](https://bits-bytes-nn.github.io/insights/agentic-ai/2026/04/05/evolution-of-ai-agentic-patterns-en.html)

### 演进时间线

```
2022  ────  2023  ────  2024  ────  2025  ────  2026
  │           │           │           │           │
  ▼           ▼           ▼           ▼           ▼
Prompt     Prompt     Context    Context    Harness
Engineering Engineering Engineering Engineering Engineering
                                              ▲
                                    当前阶段 ──┘
```

### 关键拐点

| 时间 | 拐点 | 触发因素 |
|------|------|--------|
| 2023 中 | Prompt → Context | 推理模型兴起，单一 prompt 不够 |
| 2025 中 | Context → Harness | Agent 需要在生产环境长时间运行 |
| 2026 初 | Harness 标准化 | OpenAI/Anthropic 发布官方指南 |

### 核心发现

> 工程严谨性没有消失，只是转移了位置——2026 年的关键指标不是 prompt 质量，而是 KV-cache 命中率和 harness 复杂度。

### 启示

这四年演进的最重要的教训是：**每一层范式都没有消失，而是被上层封装和自动化**。Prompt Engineering 仍然重要，但它已经成为 Context Engineering 的子集；Context Engineering 仍然重要，但它已经成为 Harness Engineering 的子集。

---

## 案例 18：InfoQ 演讲——从 Autocomplete 到 Agent 的工程实践（2026-07-05）

**来源**：[InfoQ/YouTube - Birgitta Böckeler (Thoughtworks)](https://www.youtube.com/watch?v=_R83pFpUWyM)

### 背景

Thoughtworks 的 Birgitta Böckeler 在 InfoQ 演讲中探讨了从 Prompt Engineering 到 Harness Engineering 的转变，分享了企业级 AI 编码的实践经验。

### 核心议题

1. **MCP 集成实践**：如何将 MCP 标准化地集成到企业工具链中
2. **模块化 Context**：将上下文模块化，按需组装，而非一次性加载
3. **成本现实**：AI 编码的成本约为 **$380/天/开发者**——需要量化 ROI
4. **子 Agent 研究流程**：通过子 Agent 进行前期研究，再由主 Agent 综合
5. **AI 安全的「致命三要素」**：
   - 模型能力越强，风险越大
   - 自主性越高，控制越难
   - 上下文越丰富，泄露面越广

### 启示

> 企业级 AI 编码不是技术问题，而是系统工程问题。Thoughtworks 的实践表明，成功的关键在于建立完整的 Harness 层——而非追求单个模型的性能。

---

*更新时间：2026-07-06*

---

## 案例 19：Addy Osmani——Agent Harness Engineering 的两层架构（2026-07-06）

**来源**：[Addy Osmani - Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)

Google 工程师 Addy Osmani 对 Harness Engineering 做了系统性拆解，提供了多个关键洞察。

### 「98% 是 Harness」的量化发现

对 Claude Code 的拆解发现约 98% 的复杂度在 harness 层，模型本身只占 2%。这是对「harness 比模型更重要」论点的最强量化支撑。

### 两层 Harness 模型

```
┌─────────────────────────────────────┐
│     多代理编排层 (Multi-Agent)       │
│  • 将多个编码会话组合为工作流         │
│  • 任务分解和会话交接                │
│  • 进度追踪和状态管理                │
└──────────────────┬──────────────────┘
                   │
┌──────────────────┴──────────────────┐
│      单会话 AI 层 (Single Session)   │
│  • 规则和技能 (Rules, Skills)        │
│  • 生命周期钩子 (Hooks)              │
│  • 子代理 (Sub-agents)               │
│  • 上下文窗口管理                    │
└─────────────────────────────────────┘
```

### HumanLayer 的诊断：失败原因是配置而非模型

> **大多数代理失败归因于「skill issues」（配置问题）而非模型权重问题。**

这个诊断意味着代理失败是可诊断和可修复的工程问题——改进方向是优化 harness 配置，而非等待更强模型。

### 启示

- 98% 的比例意味着 harness 优化的 ROI 远超模型选择
- 两层架构提供了 harness 设计的分解策略
- 配置问题的诊断需要系统化的可观测性支撑

---

## 案例 20：OpenAI Agents SDK 下一代——Harness 与计算平面分离（2026-07-06）

**来源**：[OpenAI - The Next Evolution of the Agents SDK](https://openai.com/index/the-next-evolution-of-the-agents-sdk)

### 架构升级

OpenAI 为 Agents SDK 引入了 model-native harness，核心创新是将 harness（控制平面）与计算层（沙箱执行）分离：

```
┌────────────────────────────────────┐
│       Harness (Control Plane)       │
│  • Agent 指令、MCP 集成              │
│  • Skills、AGENTS.md                │
│  • 编排逻辑                          │
└───────────────┬────────────────────┘
                │
┌───────────────┴────────────────────┐
│      Compute Plane (Sandbox)        │
│  • 安全隔离执行                      │
│  • 持久性和可扩展性                  │
│  • Shell / Apply Patch              │
└────────────────────────────────────┘
```

### 与 Anthropic 架构的趋同

这与 Anthropic 的 Managed Agents 架构高度一致——两家公司独立趋同于控制/计算平面分离的架构模式。

### 关键意义

1. **MCP 原生集成**确认 MCP 作为 Agent-Tool 标准协议
2. **AGENTS.md** 被 OpenAI 采纳，进一步巩固其作为 Agent 指令标准的地位
3. **Harness/计算分离**使 harness 可独立演进，适配不同计算后端

---

## 案例 21：Faros.ai——Harness Engineering 成熟度模型与五层架构（2026-07-07）

**来源**：[Faros.ai - Harness Engineering](https://www.faros.ai/blog/harness-engineering)

### 核心贡献：AI 工程三阶段成熟度模型

Faros.ai 提供了一个清晰的成熟度演进框架：

| 阶段 | 名称 | 核心活动 | 产出 |
|------|------|----------|------|
| Stage 1 | Prompt Engineering | 写好提示词 | 更好的单次输出 |
| Stage 2 | Context Engineering | 管理上下文窗口 | 减少 hallucination |
| Stage 3 | Harness Engineering | 构建运行时基础设施 | 生产级可靠性 |

### 五层 Harness 架构

Faros.ai 将生产级 Harness 拆解为五层，每层解决不同的可靠性问题：

```
┌──────────────────────────────────┐
│  Layer 5: 可观测性 (Observability)│
│  监控、日志、调用链追踪             │
├──────────────────────────────────┤
│  Layer 4: 护栏 (Guardrails)       │
│  安全边界、行为约束、权限控制        │
├──────────────────────────────────┤
│  Layer 3: 上下文与记忆 (Context)   │
│  信息传递、持久化、跨会话状态        │
├──────────────────────────────────┤
│  Layer 2: 验证循环 (Verification)  │
│  输出质量检查、错误检测和纠正        │
├──────────────────────────────────┤
│  Layer 1: 工具编排 (Tooling)      │
│  Agent 与工具的交互、失败恢复        │
└──────────────────────────────────┘
```

### 基线指标驱动的投入决策

Faros.ai 建议先量化再决定投入方向：

- 每个 merged PR 的成本
- Agent 辅助 PR vs 纯人工 PR 的合并时间
- 审查速度（PR 创建到 merge 的周期）
- 每开发者算力支出

### 启示

- **五层模型**提供了 Harness 完备性的系统化检查框架
- **先量化再投入**避免了盲目建设的常见陷阱
- **成熟度演进**为团队提供了清晰的升级路径

---

## 案例 22：The Edge Singapore——Harness Engineering 治理 AI 编码的隐性成本（2026-09-14 收录）

**来源**：[The Edge Singapore - Harness Engineering Takes On AI Coding's Hidden Costs](https://www.theedgesingapore.com/amp/digitaledge/focus/harness-engineering-takes-ai-codings-hidden-costs)（2026-09-11）

### 核心内容

- 探讨 AI 编码 agent 的隐性成本——token 消耗、维护负担、失败重试——如何通过 harness engineering 系统治理
- 标志性意义：该术语首次进入**主流商业科技媒体**视野（此前传播集中在工程博客与学术圈）

### 与既有条目的关系

- 与 #56（Azure 经济学）、案例 21（Faros 基线指标）共同支撑「harness 投入需成本量化」的论述线
- 传播链路：Hashimoto 结晶术语（2026-02）→ 学术综述（09 月三连）→ 商业媒体（本条）——领域外溢的媒体层信号

---

## 案例 23：英格兰银行——监管机构视角的 Harness Engineering（2026-09-14 收录）

**来源**：[Bank of England - Frontier AI: Harness Engineering](https://www.bankofengland.co.uk/research/fintech/frontier-ai-information-sharing-forum/frontier-ai-harness-engineering)（2026-09-02）

### 核心内容

- 英格兰银行 Frontier AI 信息共享论坛发布 harness engineering 专题，官方摘要："概述设计 harness 时涉及的实际考量——以受控、可用的方式将前沿 AI 能力应用于网络防御"
- **监管机构正面介入 harness 设计话题**，是该领域走向成熟的标志性信号

### 与既有条目的关系

- 与案例 22（商业媒体）、#57（Oracle 生产实践）构成 9 月「工程界 → 商业界 → 监管界」三层递进：harness engineering 从工程方法论升级为跨行业公共议题
- 「受控、可用」的表述与 #43（Anthropic 安全封顶爆炸半径）、案例 12（长时运行 harness 设计）的安全主线一致——监管层关注点与工程前沿收敛于同一批机制

---

## 案例 24：Latent Space 双篇——OpenAI Dark Factory 与「为人类注意力搭 Harness」（2026-09-15 收录）

**来源**：[Latent Space - Extreme Harness Engineering for Token Billionaires](https://www.latent.space/p/harness-eng)（2026-08-30）、[The Evolution of the Agent Harness](https://www.latent.space/p/attention-interface)（2026-09-09，Dan McAteer）

### 核心事实与论点

- **Dark Factory**（OpenAI 内部）：1M 行代码、日烧 10 亿 token、**0% 人工代码、0% 人工审查**——harness 极限压榨的标杆案例，自动化程度超过所有公开案例（含案例 12/20 的长时运行实践）
- **注意力 harness** 论点：模型正在把 harness 能力吸收进权重，harness 的终局不是「为模型搭脚手架」，而是「**为人类注意力搭 harness**」——人机界面从控制模型转向过滤与调度人的关注点

### 案例启示

1. 0% 人工审查成立的前提是 eval 基建（04 #58 Google 视角）+ 护栏（#43）足够厚——审查没有消失，而是从人工转移到 harness 层
2. 「注意力 harness」为 harness-as-a-service（01 章 09-14 补充）之后的下一阶段提供想象空间：当模型自管上下文成为默认，harness 的差异化价值转向人的体验层
3. 与案例 21（成熟度模型）对照：Dark Factory 相当于成熟度顶格（全自动 + 无审查），可作为组织自评的极限参照系

---

## 案例 25：mega.dev——5 天重造 4 年老应用，塑造环境让 agent 脱离人类工作（2026-09-16 收录）

**来源**：[mega.dev - Towards Autonomous Product Development](https://mega.dev/autonomous-product-development)（2026-09-08 发布，HN 2026-09-14 再热）

### 核心事实

- 用多 agent 并行在 **5 天内重建了一个开发 4 年的应用**
- 方法论核心：**塑造环境让 agent 脱离人类工作**——常驻云端、真实资源限额、把团队上下文外化为 harness 可读取的工件，而不是把人拴在监督环节上

### 案例启示

1. 「把人从监督环节拿出来」与 Dark Factory（案例 24）0% 人工审查同向，但路径不同：Dark Factory 靠 eval+护栏堆厚度，本案例靠环境塑造（常驻云端 + 真实限额 + 上下文外化）
2. 「团队上下文外化为 harness 可读工件」与 #59（Termdock 文件化路径）、#62（经验沉淀为 evals）共同指向：组织知识必须变成 agent 可消费的格式才能参与自动化
3. 与案例 11/24 并列为「小团队/短周期大规模产出」谱系的新数据点，可作为评估自身 harness 成熟度的参照案例

---

## 2026-10-06 补充：Cohere North 2 —— 预算与记忆成为 harness 一等能力（闭源企业向新样本）

**来源**：gnews RSS 多源（Unite.AI / VentureBeat，2026-10-05 前后）

### 线索：Cohere 发布 North 2，重新设计 agent harness

- 三要素：**重新设计的 harness + 内建记忆系统 + token 预算控制**（VentureBeat 标题即「puts AI agents on a budget and gives them a memory」），面向企业私有化部署
- 与开源侧对照：AWS Strands（10-05 在册）主打多模型 + 成本（−77%），DeepSeek Harness 桌面化 + 插件开放；North 2 把「预算控制」做成 harness 内建能力——**token 成本治理正从「框架外运维」前移为「harness 内建」**
- 去重注：MarkTechPost/GIGAZINE 今日多篇「DeepSeek Harness v0.2 桌面版」为 10-02 在册事件（Pandaily 09-30 首发）的后续扩散报道，同事件不重复入册

### 案例启示

1. 选型新增一问：预算/记忆是 harness 内建还是外挂？内建（North 2）省集成但绑供应商，外挂（显式节点 + 自建预算闸）灵活但自担工程
2. 「harness 管钱」趋势与 10-05 DeerFlow lead budget 计量、10-03 Kimchi 动态路由同向：成本治理三件套（计量/路由/预算）在闭源/开源两侧同步落地

---

## 2026-10-05 补充：ADK 凭证安全闭环 & Claude Code Mods 生态趋同

**来源**：Google ADK commits（2026-10-04）、gnews RSS 多源（2026-10-02/04）

### 线索 1：ADK 凭证安全 48h 两步走（接 10-04 在册事件 2）

- 10-03 紧急封堵 OAuth2 secrets 经 dev/run 端点外泄后，10-04 落地 **feat: session 内加密存储 Google 凭证**——从「堵出口」进入「加密静态态」，同一漏洞的纵深修复闭环
- 参照样本价值：从漏洞暴露（v2.11.0 发布次日）到端点封堵再到会话态加密，**48h 三步**——对自建 harness 的安全响应周期是可对标的节奏

### 线索 2：DeepSeek Harness v0.2.1-alpha.1 实验性兼容 Claude Code Mods（10-04）

- 第三方开源 harness 新增实验性 Claude Code Mods 兼容层（并回应「抄作业」争议）——**Claude Code 生态（mods/skills/hooks）正在被第三方当作事实兼容目标**
- 与在册 #90（agent 环境工程岗位化）、#93（七大原语选型）同向：当原语足够标准化，生态间会出现「兼容层」这种趋同产物；反向印证「投原语而非投单一 harness」的选型策略
- 配套生态动态：Agent37 打平价多 harness 沙箱（Claude Code/Codex/Hermes/OpenClaw 同笼，10-03）、MIT/Sakana 用 LLM judge 降自改进 agent 评测成本（10-02）、Meta/Duke/UC Davis self-improving branches 自动优化 harness（10-03）——工具层/评测层/优化层各自分层成熟

### 案例启示

1. 安全修复看「是否闭环」而非「是否打补丁」：ADK 两步走示范了出口封堵 ≠ 态势安全，会话态里的凭证也要加密
2. 生态兼容层的出现是原语标准化的领先指标：评估自建 harness 时优先采用已趋同的原语（skills/hooks/subagents 形态），降低未来迁移成本
3. harness 优化的自动化链条成形：LLM judge 降评测成本 + self-improving branches 自动搜索配置——「人调 harness」正在被「harness 自调」补充

---

## 2026-10-04 补充：安全事件双响（配置投毒劫持 / OAuth 凭证泄漏）与「harness 攻击面」浮出水面

**来源**：[GitLab - How a poisoned config can hijack an AI coding agent](https://about.gitlab.com/blog/)（2026-10-02）、Google ADK commits（2026-10-03）

### 事件 1：DeepSeek-Reasonix 配置投毒案例（GitLab，10-02）

- 案例演示：一个被投毒的配置文件即可**劫持整个 coding agent**——harness 的配置面（settings/规则文件/env）是新的攻击入口
- 与在册安全线（#43 护栏、09-25 在册 企业活动审计链路）互补：此前条目讲「防 agent 误伤」，本条讲「防人/供应链投毒 harness」——威胁模型扩了一维

### 事件 2：Google ADK OAuth2 secrets 泄漏修复（v2.11.0 后 10-03 紧急 fix）

- ADK 修复：OAuth2 secrets 会经 `/run`、`/run_sse`、`/run_live`、dev server、session endpoints **外泄**——框架自带的 dev/run 端点成为凭证泄漏通道
- 同日 ADK 还修复 `GoogleOidcVerifier` 要求 boolean `email_verified` claim（验证链又一洞）
- 启示：**harness 的每个端点/会话机制都是 secret 的潜在出口**，升级框架版本要盯安全 fix，不只是 feature

### 案例启示

1. 「harness 攻击面」应与「harness 能力面」同权重进设计评审：配置文件、端点、会话状态、工具输出都是不可信输入
2. 两条事件同周出现（第三方面研究 + 一线框架紧急修复）说明 harness 安全已从理论议题进入「必须打补丁」阶段
3. 对自建 harness 的 checklist 增项：配置文件完整性校验、dev 端点默认禁用/鉴权、secrets 永不进会话序列化路径

---

## 2026-10-07 补充：ARC-AGI-3 脚手架 4.9 倍增益——同模型不同 harness 的量化证据 & 框架动态速览

**来源**：[Tech Times - ARC-AGI-3 Scaffolding Beats Model Upgrades](https://www.techtimes.com/articles/328542/20261006/arc-agi-3-scaffolding-beats-model-upgrades-same-ai-two-settings-49x-score-gain.htm)（2026-10-06）、GitHub API（2026-10-07 实测）

### 案例：ARC-AGI-3 榜单——scaffolding 差异 > 模型差异

- ARC-AGI-3 Kaggle 社区榜单 10-04 达到 **55.89% RHAE**，几乎翻倍 09-30 Milestone 2 冠军的 27.9%——顶级提交所用**模型几乎相同**，得分差全部来自 scaffolding/harness 设置（约 4.9 倍差距）
- 与 #92（Osmani：Terminal Bench 2.0 上 Viv 换 harness 从 Top 30 到 Top 5）、案例 23 等构成第三条独立量化证据线：**harness 是当前最大的免费性能杠杆**，且首次出现在推理基准（ARC-AGI-3）而非 coding 基准上——外推到通用 agent 场景
- 启示：评测 agent 时先固定模型、扫描 harness 配置，再谈换模型；benchmark 报告应披露 harness 配置，否则分数不可比

### 框架动态（2026-10-06 快照，GitHub API）

- **LangGraph 1.2.14**（10-06 当日发布）：sdk-py 0.4.6 同步发版，修复 thread_id/assistant_id percent-encode——stream 请求中特殊字符 ID 的编码边界
- **deer-flow**（83.4K⭐）：连续 harness 细节修复——bash exit marker 在 budget rewrite 后保持末位（输出解析不破）、memory 排除无效/原始 assistant tool calls、shutdown 时 drain pending notify
- **openai-agents-python**（v0.23.1）：早期拒绝不支持的 Redis Cluster session、computer-use 安全检查警告、**deferred approval 历史跨 resume 持久化**——审批态是会话状态的一部分
- **google/adk-python**（v2.11.0 后）：修复**孤儿 function_call 永久阻塞 compaction**（上下文压缩被单个悬空调用卡死）；**要求 Python 3.11+**（drop 3.10）
- **BMAD-METHOD**（v6.12.1）：**Toolsmith 模块取代 BMad Builder**（10-06）——方法论框架也在把「工具生成」产品化
- **superpowers**（296.0K⭐）：昨日无新 commit，v6.4.2（09-25）

### 案例启示

1. 「scaffolding > 模型升级」已有三条独立量化证据（Viv/Terminal Bench、OpenAI 百万行代码库、ARC-AGI-3 榜单），跨 coding 与推理两域——harness 投入回报论证可以引用成组证据而非单例
2. 框架侧的修复方向高度趋同：会话状态完整性（deer-flow exit marker、agents-python 审批历史、ADK 孤儿调用）——「状态机正确性」正在成为 harness 工程的核心修炼
3. adk-python drop 3.10 提醒：harness 框架的运行时基线在快速上移，依赖锁定策略要预留升级窗口

---

## 2026-10-08 补充：Anthropic「Managed Agents」meta-harness——用通用接口对冲 harness 过期风险 & 框架动态速览

**来源**：[Anthropic Engineering Blog - Scaling Managed Agents](https://www.anthropic.com/engineering/managed-agents)（2026-10 上旬）、GitHub API（2026-10-08 实测）

### 案例：Anthropic 官方确立 meta-harness 架构方向

- Anthropic 工程博客新文：Managed Agents 是一种 **meta-harness**——不预设 Claude 需要什么 harness，而是提供通用接口容纳多种 harness（Claude Code、task-specific harness 等），「matching Claude's intelligence over time」
- 官方论点：**harness 编码了「模型自己做不到什么」的假设，这些假设随模型进步而过时，需要频繁重审**——为 #89 的「随模型减负」判据提供官方背书，详见 04 章 #96
- 职责分层：会话存可恢复上下文，任意上下文管理放 harness 层；事件进模型前可转换以实现高 prompt cache 命中率——上下文工程从「写死在主循环」变为「可插拔转换管道」

### 行业动态速览（Google News，2026-10-06~08）

- **Microsoft Agent Lightning v1.0**：3,500 行代码的轻量 agentic RL 框架，直接在真实 harness 环境中训练 agent（非 sandbox 假环境）——「训练时用真 harness」与「推理时 harness 决定表现」正在合流
- **Google EnvHarness 开源**：agent 训练的环境 harness 框架（DataDrivenInvestor 报道），大厂开始把 harness 作为训练基建开源
- **GitLab**：agent 流水线使软件任务重试次数至多降 45 倍，归因 harness 层验证与反馈循环——与 #95 重试语义、ARC-AGI-3 4.9× 证据线同向
- **Apple 研究**：极简单 agent 在 ML 工程任务上持平或胜过多 agent 系统——再次提醒「多 agent ≠ 更强」，harness 结构要按任务域裁剪
- **Together Link Beta**：免费 CLI 将 Kimi K3、GLM 5.3 等开源模型接入 Claude Code/Codex/OpenCode——第三方模型「借壳」成熟 harness 生态成为趋势（与 DeepSeek 兼容 Claude Code Mods 同向）

### 框架动态（2026-10-08 快照，GitHub API，since 10-07 UTC）

- **CrewAI 1.15.24**（10-07 当日发布）：`crewai eval` 在 agent 运行时输出 markdown brief（#7907）；flow 实验性 turn/reply 身份 + 后台回复排队（#7909/#7911）——flow 事件模型在向可观测性演进
- **LangGraph cli 0.4.33**（10-07）：修复 astream_events 在 v1/v2 丢 control/interrupts（#9219）；CLI 新增 `--image-uri` 部署已推镜像、`deploy listeners list`
- **deer-flow**（83.5K⭐，24h 10+ commits）：skillscan 检测 fine-grained PAT/Google API key 并扫描 .zsh（#6423）——安全扫描扩面；批处理韧性（暂停态跨单项重试保持 #6417、同线程批量结果有界检查 #6416）；sandbox 端口分配止于 TCP 上限（#6424）
- **google/adk-python**（v2.11.0 后，24h 10+ commits）：轻量安装 Gemini Enterprise SDK（免 GAPIC stack）；Gemini 3+ 允许 sub-agents 用内置搜索工具；文档补 ToolCallIntegrityPlugin 与 internal event metadata——「工具调用完整性」被产品化
- **openai-agents-python**（v0.23.1）：run 级工具异常格式化（#5326）、callback 失败后保留 tool outputs（#5318）——错误路径不吞输出
- **BMAD-METHOD**（53.9K⭐）：无昨日新 commit，v6.12.1（10-04）；**superpowers**（296.4K⭐）：无昨日新 commit，v6.4.2（09-25）

### 案例启示

1. meta-harness（接口化、可插拔）是「薄 harness 哲学」（#94）的架构化终局：薄的对象从 harness 本身转移到 harness 与运行时的接口上
2. 训练侧（Agent Lightning、EnvHarness）开始把真实 harness 纳入 RL 环境——「harness 差异可学习」意味着 harness 工程经验可能被模型侧吸收，长期或改变 harness 投入回报结构
3. 框架安全扫描扩面（deer-flow skillscan 扫 .zsh、检测 PAT）提示：harness 运行在开发者的真实 shell 环境，凭证泄露面随 agent 普及同步扩大

## 2026-10-09 补充：Claude Haiku 5.5 定价重构 harness 经济学 & 框架并发正确性修复潮

**来源**：Google News/Tavily（2026-10-07~08）、GitHub API（2026-10-09 实测，since 10-07 21:30 UTC）

### 案例：定价变化改写 subagent 并发经济学

- **Claude Haiku 5.5 发布**（10-07）：1M 上下文、输入 $0.10/M tokens（最高降 90%）、性能对标 GPT-6 Luna；**Sonnet 5.5 缓存读取价 -50%**——小模型分层调度 + 高缓存命中的成本收益双升，详见 04 章 #97
- **Claude Max/Team 订阅新增 API credits**（10-08，Max 最高 $200/月，不可用于 Claude Code 本身）：订阅与 API 打通，SDK/subagent 批量任务多了 subsidized 额度渠道
- **OutSystems Agent Experience 向 Claude Code/Cursor 开放**（10-08）：编码 harness 接入企业低代码平台，harness 生态从开发者工具向企业应用平台渗透
- **Claude Code 2.1.293 回退两天前的 cloud-session 修复**（10-08）：fix→regression→revert 循环再现——harness 升级需 pin 版本 + 盯 changelog，不要盲升
- **Agent 互评工具评测站上线**（10-08，Claude Code/Codex 已在发帖）：工具质量成为 harness 竞争新维度，agent-to-agent 评测生态萌芽
- **OpenAI DevDay 2026 聚焦 agent 任务管理**（10 月上旬）：harness 竞争焦点从模型能力转向任务编排/执行管理

### 框架动态（2026-10-09 快照，GitHub API，since 10-07 21:30 UTC）

- **deer-flow**（83,527⭐，24h **48 commits**，最活跃）：fix(subagents) stop drain 期间 fence 批次 poller 启动（#6517）；fix(scheduler) one-time task 在 early trial 后保留调度（#6512）；fix(sandbox) 本地 provider 遵守 sandbox.environment（#6463）；fix(auth) 登录锁定计数入库存（Gateway 多副本一致，#6501）
- **google/adk-python**（21,748⭐，24h **30 commits**）：fix: confirmed tool 每次审批只执行一次（审批幂等）；feat(plugins) BigQueryLoggerConfig 新增 on_schema_error/on_schema_ready 钩子；fix: after_model_callback 替换 response 时继承 usage_metadata（用量元数据不丢）
- **CrewAI 1.15.25**（10-07 发布，59,470⭐）：fix: rwlock 获取被中断时保持所有权（#7944）；fix(cli) log-tasks-outputs 显示真实任务输出（#7936）
- **LangGraph**（42,912⭐，4 commits，cli 0.4.33 后）：fix(checkpoint) 对象无法 rebuild 时保留序列化数据（#9251）；fix(prebuilt) ToolNode 不吞非法 resume 值（#9232）；fix(exit-mode) delta writes 不落 null task id（#9229）
- **openai-agents-python**（29,916⭐，2 commits）：[sandbox-hardening] macOS 本地沙箱封锁 Launch Services（#5335）；fix: 会话写入按 API 限额批量（#5334）
- **BMAD-METHOD**（53,953⭐）/ **superpowers**（296,541⭐）：无昨日新 commit（v6.12.1 / v6.4.2）

### 案例启示

1. **并发正确性成为本周框架修复主题**：deer-flow poller fence、ADK confirmed-tool 幂等、CrewAI rwlock 所有权、LangGraph null task id——subagent/工具并发路径的竞态与重复执行是当前 harness 实现的共同痛点，自有 harness 应专项审计「审批后重复执行」与「drain 期间新任务准入」两类竞态
2. **定价重构后重算 harness 账本**：Haiku 5.5 + 缓存减半改变了 subagent 分层、长会话缓存策略的成本判据（#97）——每季度重跑一次成本模型，营销折扣倍数按实际 token 构成核实
3. **发布回退事件**（Claude Code 2.1.293）提示 harness 依赖管理：生产环境 pin 具体版本，升级前核对 changelog 与回退历史，而非追新
