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
