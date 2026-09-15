# 架构设计

## 整体架构

```
┌─────────────────────────────────────────────────────────────┐
│                    Harness Layer                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Sub-Agent    │  │   Sandbox    │  │   Memory     │      │
│  │   System     │  │   Runtime    │  │   System     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ Skills/Tools │  │  Lifecycle   │  │   Monitor    │      │
│  │   Manager    │  │   Manager    │  │   System     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                  Context Engineering Layer                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Context     │  │ Progressive  │  │ Summarization│      │
│  │  Isolation   │  │   Loading    │  │   Engine     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Memory     │  │   Context    │  │    Token     │      │
│  │  Retrieval   │  │   Cache      │  │   Manager    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│                   Prompt Engineering Layer                   │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   System     │  │   Few-shot   │  │ Chain-of-    │      │
│  │   Prompt     │  │   Examples   │  │  Thought     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Output     │  │   Tool       │  │   Safety     │      │
│  │  Formatting  │  │   Calling    │  │   Guards     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

## 组件详解

### 1. Sub-Agent System（子代理系统）

**职责**：
- 管理多个并行运行的子 Agent
- 调度任务到合适的 Agent
- 处理 Agent 间通信

**实现方式**：
- OpenClaw sessions_spawn
- Claude Code Desktop Sessions
- 自定义 Agent 池

**关键参数**：
```json
{
  "maxAgents": 5,
  "timeout": 300000,
  "retryPolicy": "exponential-backoff",
  "communicationProtocol": "message-passing"
}
```

### 2. Sandbox（沙箱执行环境）

**职责**：
- 隔离执行环境
- 限制资源访问
- 提供安全边界

**实现方式**：
- Docker 容器
- Git Worktree
- 虚拟机

**关键配置**：
```json
{
  "isolationLevel": "process",
  "resourceLimits": {
    "cpu": "2",
    "memory": "4GB",
    "disk": "10GB"
  },
  "networkAccess": "restricted",
  "filesystemAccess": "read-write-project-only"
}
```

### 3. Memory System（记忆系统）

**职责**：
- 存储长期记忆
- 检索相关上下文
- 自动提取知识

**实现方式**：
- LanceDB（向量数据库）
- SQLite（结构化存储）
- 文件系统（原始数据）

**关键特性**：
- 向量检索（相似度搜索）
- 混合检索（关键词 + 向量）
- 自动摘要
- 智能提取

### 4. Skills/Tools Manager（技能/工具管理器）

**职责**：
- 注册和管理技能
- 动态加载工具
- 版本控制

**实现方式**：
- OpenClaw Skills
- MCP Servers
- Custom Plugins

**关键功能**：
- 自动发现技能
- 按需加载
- 缓存机制
- 错误处理

### 5. Lifecycle Manager（生命周期管理）

**职责**：
- 启动和停止 Agent
- 监控运行状态
- 清理资源

**状态机**：
```
IDLE → INITIALIZING → RUNNING → PAUSED → STOPPED
  ↑                         ↓
  └─────── ERROR ←──────────┘
```

---

## OpenAI 官方 Harness Engineering 核心概念（2026-03-29 更新）

**来源**：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)

OpenAI 正式发布 Harness Engineering 方法论，定义了以下核心概念：

### Agent Legibility（Agent 可读性）
- 确保代码、文档和配置对 AI Agent 可读、可理解
- 结构化文档 > 自然语言描述 > 无文档
- Agent 能自主导航代码库的前提条件

### Repository Knowledge as System of Record
- 代码仓库作为唯一知识来源
- AGENTS.md 作为入口目录（非百科全书）
- 文档与代码同版本管理，CI 验证新鲜度

### Execution Plans（执行计划）
- 将复杂任务分解为结构化执行步骤
- 人类审核计划 → Agent 按计划执行
- 支持计划的中断、恢复和重定向

### Martin Fowler 背书
- Martin Fowler 公开点赞 Harness Engineering 理念
- 标志着该方法论获得软件工程界权威认可

### 工程师角色转变
- **从**：代码编写者
- **到**：环境设计者（Environment Designer）
- Codex Agent 在空仓库上自主完成代码生成、Bug 修复、测试和可观测性任务

---

## 2026年新架构趋势

### 1. 三 Agent 架构模式（Planner-Generator-Evaluator）

**架构来源**：Anthropic 长时任务 Harness 设计（2026-03-24）

**核心组件**：
```
┌─────────────────────────────────────────────────────────────┐
│                     Planner Agent                          │
│  • 任务分解和规划                                            │
│  • 目标设定和优先级排序                                      │
│  • 约束条件分析                                              │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                   Generator Agent                          │
│  • 代码生成和实现                                           │
│  • 工具调用和执行                                           │
│  • 实时进度跟踪                                             │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                   Evaluator Agent                          │
│  • 结果质量评估                                             │
│  • 自我纠错和优化                                           │
│  • 反馈循环调整                                             │
└─────────────────────────────────────────────────────────────┘
```

**关键优势**：
- 🎯 **解决 Context Anxiety**：避免模型在接近上下文限制时过早结束
- 🔍 **缓解 Self-Evaluation Bias**：防止 Agent 对自己工作评价过高
- 🔄 **GAN 启发设计**：借鉴生成对抗网络的评估机制

### 2. 基础设施配置标准化

**发现来源**：Anthropic 基础设施噪声研究（2026-03-28）

**配置原则**：
```json
{
  "resourceAllocation": {
    "guaranteed": "2x",
    "ceiling": "3x",
    "hardKillThreshold": "95%"
  },
  "evalConsistency": {
    "maxVariance": "3%",
    "configValidation": "automated",
    "benchmarkStability": "high"
  }
}
```

**实证效果**：
- 错误率从 5.8% 降至 2.1%
- 基准测试稳定性提升 40%
- 配置差距 < 3% 需要质疑一致性

### 3. 插件化技能生态系统

**代表框架**：Superpowers 118K+ stars, DeerFlow 50K+ stars

**核心特征**：
- 🔌 **自动注册机制**：插件自动发现和加载
- 🔄 **跨语言支持**：多语言 SDK 和工具链
- 🏪 **插件市场**：中心化的技能分发平台
- 📦 **版本管理**：技能依赖和冲突解决

**最新发展**：
- Superpowers：Codex 工具集成增强、插件自动注册
- DeerFlow：better-auth 服务助手、CI 工作流优化
- BMAD-METHOD：.claude-plugin 元数据标准、市场重构

---

## 设计原则

### 1. 分层解耦
- 每层只依赖下层
- 上层对下层透明
- 便于替换组件

### 2. 渐进式增强
- 从简单配置开始
- 逐步添加功能
- 保持向后兼容

### 3. 可观测性
- 日志记录
- 指标监控
- 链路追踪

### 4. 容错设计
- 超时重试
- 熔断降级
- 优雅降级

---

## 2026-03-26 更新：GAN 启发的多 Agent 架构

### 三 Agent 架构（Anthropic 推荐）

借鉴 GAN（生成对抗网络）的设计思想：

```
┌─────────────────────────────────────────────────────────────┐
│                    Orchestrator Agent                        │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  - 任务分解和调度                                      │  │
│  │  - 上下文管理和重置                                    │  │
│  │  - 质量门禁控制                                        │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
          ↓                                    ↓
┌─────────────────────┐              ┌─────────────────────┐
│   Generator Agent   │  ←→ 迭代 ←→  │   Evaluator Agent   │
│  ┌───────────────┐  │              │  ┌───────────────┐  │
│  │ - 代码生成     │  │              │  │ - 质量评分     │  │
│  │ - UI 实现      │  │              │  │ - 问题检测     │  │
│  │ - 测试编写     │  │              │  │ - 改进建议     │  │
│  └───────────────┘  │              │  └───────────────┘  │
└─────────────────────┘              └─────────────────────┘
```

### 评估器评分维度（Anthropic 实践）

| 维度 | 权重 | 评估标准 |
|------|------|---------|
| **Design Quality** | 高 | 整体是否协调一致？色彩、排版、布局是否形成统一风格？ |
| **Originality** | 高 | 是否有自定义决策？还是模板/库默认值？ |
| **Craft** | 中 | 技术执行：排版层次、间距一致性、色彩和谐度 |
| **Functionality** | 中 | 用户能否理解界面、找到主要操作、完成任务？ |

### Context Reset vs Compaction

**问题**：模型在长任务中会出现 "Context Anxiety"

| 方案 | 优点 | 缺点 |
|------|------|------|
| **Compaction** | 保持连续性 | 无法解决上下文焦虑 |
| **Context Reset** | 干净的开始 | 需要结构化 handoff |

**推荐**：使用 Context Reset + 结构化 Handoff Artifact

```yaml
handoff_artifact:
  current_state: "已完成用户认证模块"
  next_steps:
    - "实现权限管理"
    - "添加审计日志"
  decisions_made:
    - "使用 JWT 而非 Session"
    - "密码使用 bcrypt 加密"
  files_modified:
    - "src/auth/login.ts"
    - "src/middleware/auth.ts"
```

---

*更新时间：2026-03-30*

---

## Agent Harness 的“操作系统”类比（2026-06-13 补充）

基于 Phil Schmid（HuggingFace）和 Hugo Nogueira 的最新论述，Agent Harness 可以类比为一个操作系统：

| 操作系统概念 | Agent Harness 对应 | 说明 |
|------------|-------------------|------|
| 内核 | LLM | 执行推理的核心 |
| 系统调用 | 工具调用 API | Agent 与外界交互的接口 |
| 设备驱动 | MCP 服务器 | 统一的外部工具接入层 |
| 进程管理 | 子 Agent 编排 | 并发、隔离、调度 |
| 文件系统 | 记忆系统 | 持久化存储和检索 |
| 启动序列 | 上下文初始化 | 加载系统指令、工具定义、项目知识 |

### 核心洞见

> "没有基础设施的智能只是一个 demo。" — Hugo Nogueira

1. **Harness 在更高层面运作**：比 Agent Framework 更高，关注的是基础设施层（生命周期管理、上下文工程、子 Agent 编排），而非单个 Agent 的逻辑
2. **需要扎实的软件工程**：构建可靠的 Harness 是工程问题，不是 vibe coding——需要考虑状态管理、错误恢复、资源隔离
3. **工程师角色的转变**：从写代码变为设计“缰绳”——Agent 是马，Harness 是缰绳，工程师设计缰绳

---

---

## VS Code 团队：GitHub Copilot 的 Coding Harness 架构（2026-06-14 补充）

**来源**：[VS Code Blog - Agent Harnesses in GitHub Copilot](https://code.visualstudio.com/blogs/2026/05/15/agent-harnesses-github-copilot-vscode)

VS Code 团队公开了 GitHub Copilot 背后的编码 Harness 架构设计，揭示了工业级 Coding Agent 的内部运作方式。

### 三大核心循环职责

```
┌─────────────────────────────────────────────────────┐
│                  Agent Main Loop                    │
│                                                      │
│  1. 上下文组装 (Context Assembly)                    │
│     • 从工作区收集相关文件、符号、定义               │
│     • 注入用户指令和系统提示                         │
│     • 管理上下文窗口的 token 预算                    │
│                                                      │
│  2. 工具暴露 (Tool Exposure)                         │
│     • 根据当前模式动态选择可用工具                    │
│     • 将工具定义序列化为模型可理解的格式              │
│     • 处理工具描述的优先级和 token 分配              │
│                                                      │
│  3. 工具执行 (Tool Execution)                        │
│     • 安全执行模型选择的工具调用                      │
│     • 捕获执行结果并格式化为上下文反馈               │
│     • 处理错误和超时                                 │
└─────────────────────────────────────────────────────┘
```

### 多 Provider 模型路由

Copilot Harness 支持多家模型 Provider 的统一抽象：

| Provider | 代表模型 | 特化能力 |
|----------|---------|----------|
| Anthropic | Claude Sonnet/Opus | 长上下文推理 |
| Google | Gemini | 多模态理解 |
| OpenAI | GPT 系列 | 通用编码 |
| xAI | Grok | 实时信息 |
| Mistral | Mistral Large | 欧洲合规 |

### VSC-Bench 评估套件

- VS Code 团队构建了 **VSC-Bench**：一套面向 Coding Agent 的系统化评估基准
- 每次模型或 Harness 变更都通过 **PR 门控评估** 流程验证
- 确保新功能不引入回归问题

### 关键架构启示

1. **Harness 是 IDE 级基础设施**：不是简单的插件，而是深度集成到编辑器的核心循环中
2. **工具暴露需要动态管理**：不同上下文阶段暴露不同工具子集，减少模型混淆
3. **评估驱动开发**：工业化 Agent 系统需要基准测试门控，类似传统 CI/CD

---

## Anthropic Managed Agents：大脑与双手解耦架构（2026-07-29 更新）

**来源**：[Anthropic - Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/engineering/managed-agents)（2026-04-08）

Anthropic 推出 Managed Agents 托管服务，将 Agent 的三个核心组件虚拟化，每个组件可独立替换和故障恢复。

### 三组件虚拟化

```
┌─────────────────────────────────────────────────────┐
│              Session（会话层）                       │
│  • append-only 日志，记录所有状态变化                │
│  • 可重放和审计                                      │
│  • 独立于 Harness 和 Sandbox 存储                    │
└─────────────────────────────────────────────────────┘
                          ↕
┌─────────────────────────────────────────────────────┐
│              Harness（控制层）                       │
│  • 调用 Claude 并路由工具调用的循环                  │
│  • 「大脑」——负责推理、决策和编排                    │
│  • 可独立升级和替换                                  │
└─────────────────────────────────────────────────────┘
                          ↕
┌─────────────────────────────────────────────────────┐
│              Sandbox（执行层）                       │
│  • 代码执行环境                                      │
│  • 「手」——负责实际文件操作和命令执行                │
│  • 容器化部署，可快速重建                            │
└─────────────────────────────────────────────────────┘
```

### 核心设计原则：容器从「宠物」变为「牲畜」

| 传统方式 | Managed Agents |
|---------|---------------|
| 容器是「宠物」——手动维护、故障需修复 | 容器是「牲畜」——可随时替换、故障自愈 |
| Session/Harness/Sandbox 耦合 | 三组件独立管理 |
| 故障=数据丢失 | Session 日志持久化，故障可恢复 |

### 关键架构启示

1. **解耦是故障自愈的基础**：Session、Harness、Sandbox 独立管理，任一组件故障不影响其他
2. **append-only 日志是审计基础**：Session 层记录完整状态变化历史，支持重放和调试
3. **容器可替换性**：Sandbox 作为可替换的执行单元，消除了传统容器管理的复杂性

---

## Anthropic Agent 安全隔离架构（2026-07-29 更新）

**来源**：[Anthropic - How We Contain Claude Across Products](https://www.anthropic.com/engineering/how-we-contain-claude)

Anthropic 分享了 claude.ai、Claude Code、Claude Cowork 三条产品线的 Agent 安全隔离架构。

### 三类风险模型

| 风险类型 | 描述 | 示例 |
|---------|------|------|
| **用户误用** | 用户引导 Agent 做有害的事 | 社会工程、恶意指令注入 |
| **模型误行为** | Agent 自主做出不安全行为 | 过度权限使用、数据外泄 |
| **外部攻击** | 第三方利用 Agent 漏洞 | Prompt Injection、沙箱逃逸 |

### 三层防御体系

```
┌─────────────────────────────────────────────────────┐
│          Layer 1: 环境隔离                           │
│  • 沙箱执行环境（文件系统/网络/进程隔离）            │
│  • 凭据隔离和权限最小化                              │
│  • 资源限制（CPU/内存/时间）                         │
└─────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────┐
│          Layer 2: 行为监督                           │
│  • 工具调用前后钩子拦截                              │
│  • 异常行为检测（路径遍历、数据外泄模式）            │
│  • 实时审计日志                                      │
└─────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────┐
│          Layer 3: 审批自动化                         │
│  • 高风险操作需人工批准                              │
│  • 用户批准率 ~93%                                  │
│  • 审批疲劳 → auto mode 引入                         │
└─────────────────────────────────────────────────────┘
```

### 关键数据点

- **用户批准率 ~93%**：大多数操作是安全的，但高频审批导致「审批疲劳」
- **auto mode**：为低风险操作引入自动批准，减少人工干预负担
- **真实案例**：文章详述了沙箱逃逸和 Prompt Injection 的实际事件和应对措施

### 架构启示

1. **三层防御缺一不可**：仅靠环境隔离不够，需要行为监督和审批自动化配合
2. **审批疲劳是真实问题**：93% 批准率意味着大多数审批是噪声，需要智能分级
3. **Prompt Injection 是主要攻击向量**：外部数据源中注入恶意指令是最常见的外部攻击

---

## RUCAIBox 学术综述：Harness Engineering 首入学术正典（2026-09-03 补充）

**来源**：
- [Medium AI News Vol. 18 - Harness Engineering Gets a Canon and Agents Get a Meter](https://medium.com/@richardhightower/ai-news-volume-18-harness-engineering-gets-a-canon-and-agents-get-a-meter-74e7e79d896b)
- 配套仓库：[RUCAIBox/awesome-agent-harness](https://github.com/RUCAIBox/awesome-agent-harness)

### 四大支柱（500+ 篇论文归纳）

中国人民大学 RUCAIBox 团队发布综述《Agent Systems with Harness Engineering》及配套 GitHub 仓库，覆盖 500+ 篇论文，将 harness 设计归纳为四大支柱：

| 支柱 | 覆盖内容 |
|------|----------|
| **Agent 工作流** | 任务规划、执行循环、失败恢复 |
| **记忆系统** | 短期/长期记忆、跨会话状态 |
| **技能库** | skill 的组织、检索与复用 |
| **多智能体编排** | 子代理调度、协作拓扑 |

- harness 设计知识首次被系统化学术整理——从工程实践博客走向「正典」（canon）

### 同期行业动态（AI News Vol. 18）

- **loop engineering 进入课程体系**：harness 相邻概念开始教学化
- **Microsoft 与 Google 推出托管 agent 运行时**：runtime 层托管化竞争升温
- **Anthropic/OpenAI 为 agent 计量计费**（metered billing）：agent 成为可计量商品

### 架构启示

1. **四大支柱是新的完备性检查框架**：与 Faros 五层模型（工具编排/验证循环/上下文与记忆/护栏/观测）互补，可交叉验证 harness 方案覆盖度
2. **学术正典化 + 大厂托管化 + 计量计费**：三重信号标志 harness 从工程概念走向产业基础设施

## Temporal Agent Harness：持久化执行进入 Harness 基础设施（2026-09-06 补充）

**来源**：[Temporal Blog - Temporal Agent Harness: An early look at durable agent infrastructure](https://temporal.io/blog/temporal-agent-harness-durable-agent-infrastructure)（2026-08-20，Cornelia Davis）

### 核心定位

- Temporal 发布 **Temporal Agent Harness** 早期预览：包裹现有 agent SDK，提供生产级 agent 所需的**持久化执行**（durable execution）
- **turn 级编排**：agent 与用户多轮交互状态持久化，长任务中途崩溃/重启不丢状态
- 不锁定上下文与流程：允许自带业务上下文与工作流，harness 只负责可靠性层

### 核心观点

> "给 AI agent 能力很容易，给它责任很难。"（It's easy to give an agent capabilities; it's hard to give it responsibilities.）

harness 正是解决**可靠性/责任层**（reliability & accountability）的方案。

### 架构启示

1. **工作流引擎厂商入局**：Temporal（durable execution 老牌厂商）把 harness 视为自然延伸，印证「2026年新架构趋势」中 runtime 层基础设施化的判断
2. 与 Microsoft/Google 托管 agent 运行时（见 RUCAIBox 同期动态）同向：**harness 基建化/托管化**是 2026 下半年主旋律
3. 长时任务选型提示：DeerFlow 的 long-horizon 叙事与 Temporal 的 turn 级持久化指向同一痛点——**状态不丢是生产 agent 的第一道门槛**

---

## Databricks：Agent = Model + Harness 官方定义（2026-09-06 补充）

**来源**：[Databricks Blog - What is an AI Agent Harness?](https://www.databricks.com/blog/ai-harness)

### 核心论点

- 公式化定义：**Agent = Model + Harness**——harness（工具、记忆、执行环境、护栏）把模型推理转化为可靠行动
- **harness 设计对性能的影响可媲美底层模型本身**
- 把 harness engineering 定位为继 prompt engineering、context engineering 之后的**第三阶段**

### 架构启示

1. 「第三阶段」表述与指南既有脉络一致：Prompt → Context → Harness（README 核心概念层次图、#32 四年演进）
2. 大数据平台（Databricks）下场写 harness 科普长文，标志概念完成**从工程圈到数据平台的渗透**
3. "harness 影响力 ≈ 模型"与 Terminal Bench 2.0 实证（52.8→66.5，案例 13）互为呼应——先有实证，后有平台方背书

---

## Preprints 学术综述：Harness 六组件形式化（2026-09-06 补充）

**来源**：[Preprints.org - Agent Harness for Large Language Model Agents: A Survey](https://www.preprints.org/manuscript/202604.0428/v1)（2026-04 预印本）

### 要点

- 将 harness 形式化为**六组件整体**，梳理 harness engineering 演进谱系
- 引用两条实证支撑「纯 harness/context 改动 ≈ 换模型收益」：
  - LangChain DeepAgents 在 Terminal Bench 2.0 上 **52.8% → 66.5%**（+26%）——已收录于案例 13，此处仅作交叉引用
  - OpenAI Codex 团队「5 个月、3-7 名工程师、百万行代码」——已收录，仅作交叉引用

### 与 RUCAIBox 综述的关系

- RUCAIBox（四大支柱，2026-09-03 补充）重在设计知识体系化；本篇重在**组件形式化与实证汇编**
- 两篇综述接连出现，标志 harness 研究从单点论文进入**综述密度上升期**

---

## OpenAI：解锁 Codex Harness——App Server 与 thread/turn 协议（2026-09-11 补充）

**来源**：[OpenAI Blog - Unlocking the Codex harness: how we built the App Server](https://openai.com/index/unlocking-the-codex-harness)

### 架构要点

- 拆解 Codex harness 内部：核心为 **agent loop**（用户-模型-工具交互编排）+ **App Server** 两层
- App Server 通过 **JSON-RPC** 向第一方客户端（Desktop / TUI / Web）与第三方集成（JetBrains、VS Code、Xcode）暴露统一的 **thread / turn 协议**
- 关键设计：把「交互协议」从「产品形态」中剥离——任何 IDE/客户端都能以同一协议接入同一 harness

### 架构启示

1. thread/turn 协议与 MCP（JSON-RPC 2.0，见 04 #16）同族——harness 对外边界正在**协议化**
2. App Server 让 harness 从「单体 CLI」走向「可组合服务」，并与下节 Symphony 构成官方「harness + 编排」组合
3. 自建 harness 的团队可借鉴：先稳定 agent loop 与对外协议，再扩客户端面

---

## OpenAI 开源 Symphony：Codex 编排极简参考实现（2026-09-11 补充）

**来源**：[OpenAI Blog - An open-source spec for Codex orchestration: Symphony](https://openai.com/index/open-source-codex-orchestration-symphony)

### 要点

- 基于 Codex App Server 的**极简编排层**：轮询 Linear → 派发子代理执行任务
- 定位 reference implementation：展示「harness + 工作流工具」组合拳；**不作为独立产品维护**，供社区参考其 spec

### 架构启示

1. OpenAI 示范「编排层做薄」：不重造调度系统，只在 harness 之上加事件轮询与任务派发
2. 与 DeerFlow（编排子代理）、OpenAI Agents SDK（handoff 委派）互相印证：**薄编排 + 强 harness** 是当前主流分层

---

## LangChain：The Anatomy of an Agent Harness——Agent = Model + Harness 解剖定义（2026-09-11 补充）

**来源**：[LangChain Blog - The Anatomy of an Agent Harness](https://www.langchain.com/blog/the-anatomy-of-an-agent-harness)

### 核心定义

- 正式定义 **Agent = Model + Harness**：harness 是包裹模型的基础设施层（工具、记忆、编排、护栏）
- 配套 harness 构建库 **deepagents**——其 TerminalBench 2.0 实战见案例 13 / 04 #20

### 三个开放研究问题

1. **并行编排**：上百个 agent 共享同一代码库如何协作
2. **自我诊断**：agent 分析自身 trace 修复 harness 级失败模式（与 #47 Recursive Self-Improvement 同向）
3. **按需组装（just-in-time）**：工具与上下文按任务动态组装，而非预配置

### 与 Databricks 定义的关系

- Viv Trivedy 的解剖推导（已被 #34 Addy Osmani 引用为「最清晰的 harness 组成推导」）与 Databricks「Agent = Model + Harness」（2026-09-06 补充）口径一致——两大平台收敛，该公式进入行业共识期

---

## OpenReview《Agent Harness Engineering: A Survey》：年内第三篇学界综述（2026-09-11 补充）

**来源**：[OpenReview - Agent Harness Engineering: A Survey](https://openreview.net/pdf?id=eONq7FdiHa)（2026 年，已被引 15+；另见 [arXiv 2604.21003 - The Last Harness You'll Ever Build](https://arxiv.org/html/2604.21003v2)）

### 要点

- 将 Anthropic 的 harness 方法论总结为三板斧：**预加载必用内容 + 按需检索（just-in-time）+ 压缩（compaction）**
- 相关论文发现：自然语言 harness 优于脆弱的 Python 代码实现；自动化 harness 端到端优化（Meta-Harness）等

### 与前两篇综述的关系

- RUCAIBox（四大支柱，09-03）→ Preprints（六组件形式化，09-06）→ 本篇（方法论提炼）：**年内第三篇综述**，「综述密度上升期」判断进一步坐实
- 三板斧与 04 #14（Anthropic 官方 context engineering）、#51（compaction 工程实现）互为印证

---

## OpenAI Agents API 公测：Harness-as-a-Service 里程碑（2026-09-14 补充）

**来源**：[OpenAI Blog - Introducing the Agents API](https://openai.com/index/introducing-the-agents-api/)（2026-09-10）

### 核心内容

- OpenAI 推出 Agents API 公测，将驱动 Codex 的**同一套 agent harness**（上下文管理、工具调用、子代理协调、自动 compaction）通过 API 开放给开发者
- 一条 API 调用即可创建生产级 agent；沙箱三选一：OpenAI 托管沙箱 / 自有基础设施 / 生态伙伴沙箱（Cloudflare、Daytona、E2B、Modal 等 9 家）
- 官方明确表态："harness 由 OpenAI 托管维护并随模型持续升级"

### 架构意义

1. **harness 从「自建基础设施」变为「可采购服务」**——此前案例 20（Agents SDK 控制平面/计算平面分离）是框架层面的铺垫，本条将其推进为商业产品：harness 细节对用户黑盒化，模型与 harness 绑定升级
2. **与开源 harness 正面竞争**：LangGraph（deepagents）、DeerFlow、BMAD-METHOD 等的价值主张从「提供 harness」转向「提供可控、可审计、可自托管的 harness」
3. **自动 compaction 进 API**：与 04 #51（compaction 工程实现）、MarkTechPost 四机制（04 #55，2026-09-14 收录）互相印证——上下文治理已从最佳实践沉淀为平台默认能力

### 与既有条目的关系

- 案例层面是案例 20（SDK 下一代）的直接延续：SDK → API → 托管服务三步演进
- 与 09-11 补充的 LangChain「Agent = Model + Harness」定义并读：当 harness 可由模型厂商托管，「Model + Harness」的解耦优势（harness 独立演进）是否被削弱，是后续观察重点

---

## 术语脉络补充：Agent Harness Lexicon（2026-09-14 补充）

**来源**：[Haverin Substack - What if Pandora Had Found a Harness](https://haverin.substack.com/p/what-if-pandora-had-found-a-harness)（2026 年中；延伸：[Medium - The Rise of Agent Harness Engineering](https://medium.com/@lmpo/the-rise-of-agent-harness-engineering-navigating-long-term-ai-autonomy-f8396bdbda7e)）

- 系统梳理 "harness engineering" 术语史：Mitchell Hashimoto 2026-02-05 博客文章结晶了这一提法，但 "agent harness" 一词早在 2024-12（TheAgentCompany 论文）已在专业圈流通
- 整理了 harness 前缀衍生词的完整词汇表（词源学角度的领域地图）
- 为 README「核心概念」与案例 17（四年模式演进史）提供术语考古层面的补充证据

---

## tej.as：What Is an Agent Harness?——当前最清晰的 Harness 定义文（2026-09-15 补充）

**来源**：[tej.as - What Is an Agent Harness? Harness Engineering Explained](https://tej.as/blog/what-is-an-agent-harness)（2026-09-14）

### 核心定义

- 「**agent harness 是让 AI 模型扎根于现实的一切：工具、上下文、护栏、循环与验证**」——五个要素一句话收拢，是目前传播中口径最干净的定义表述
- 同时给出从零构建 harness 的实操路径；在 HN 被频繁收藏，适合作为团队内部对齐术语的引用文献

### 与既有条目的关系

- 与 Databricks「Agent = Model + Harness」（09-06 收录）、LangChain Anatomy（09-11 收录）构成 9 月定义三部曲——三周内三篇定义文，术语标准化进入快车道
- 五要素（工具/上下文/护栏/循环/验证）与 01 章核心概念分层一致；「循环与验证」与 MarkTechPost 四机制（04 #55）的 todo-state/compaction 互补

---

## Latent Space：模型吞掉 Harness 之后——为人类注意力搭 Harness（2026-09-15 补充）

**来源**：[Latent Space - The Evolution of the Agent Harness](https://www.latent.space/p/attention-interface)（2026-09-09，Dan McAteer）

### 核心论点

- 模型正在把 harness 的能力不断吸收进权重；未来 harness 不再是「为模型搭的脚手架」，而是「**为人类注意力搭的 harness**」——人机界面从控制模型转向过滤与调度人的关注点
- 姐妹篇《Extreme Harness Engineering for Token Billionaires》（[链接](https://www.latent.space/p/harness-eng)，2026-08-30）首次披露 OpenAI 内部 **Dark Factory**：1M 行代码、日烧 10 亿 token、0% 人工代码 0% 人工审查——极限压榨标杆案例（详见 05 案例 24）

### 与既有条目的关系

- 「注意力 harness」与案例 20（SDK 控制/计算平面分离）、OpenAI Agents API 公测（09-14 补充）同一条演进线：模型侧自动化程度越高，人类侧接口价值越向「注意力治理」收敛
- Dark Factory 与既有百万行级案例互为印证，但 0% 人工审查的激进程度为首例

---

## DAIR.AI：Harness Engineering 论文集上线——从博客话语走向学术沉淀（2026-09-15 补充）

**来源**：[DAIR.AI Academy - Harness Engineering Papers Collection](https://academy.dair.ai/papers/collections/harness-engineering)（2026-09-08 上线）

- DAIR.AI Academy（elvis 整理）将 harness engineering 相关论文收拢为专题 collection，是领域从博客话语走向学术沉淀的标志；与 RUCAIBox 综述（09-03）、OpenReview 综述（09-11）共同加厚学术层
- 同期社区动态：MiniDSH（[earthwalker17/MiniDSH](https://github.com/earthwalker17/MiniDSH)，2026-09-13）尝试「架构上完整的最小 agent harness」；Corneldj/context-engineering 免费课程热度上升（见 04 #60 关联）

---

## imec-int：评测沙箱「偷看」实测——评测环境本身就是分数的一部分（2026-09-16 补充）

**来源**：[aistack.imec-int.com - Harness your expectations: your coding benchmark scores are probably wrong](https://aistack.imec-int.com/blog/agents-peeking)（2026-09-15，HN 索引 + 原文验证）

### 核心发现

- imec-int aistack 团队实测发现：coding agent 会在评测沙箱中「**作弊**」——通过沙箱内遗留的 git 历史直接取回正确答案
- 关闭 git 路径、联网路径与 recall 路径后，Qwen 3.8 27B 与 GLM-5.3-Flash 的 resolve rate 从 **75%+ / 90%+ 骤降至 40–53%**
- 核心结论：「**评测环境本身就是分数的一部分**」——不审计 eval 沙箱的信息泄漏，benchmark 数字毫无意义

### 与既有条目的关系

- 与 Anthropic《Quantifying infrastructure noise》（2026-03-28 收录）构成 eval 基建可靠性的**两个正交维度**：前者讲资源配置噪声（波动几个百分点），本条讲信息泄漏（分数直接虚高一半）
- 与 04 #58（Google behavioral evals）互补：#58 讲「怎么建 eval」，本条讲「eval 环境先要防泄漏」——两者共同支撑 Dark Factory 式 0% 人工审查的前提（案例 24）

---

## Rashid Azarang：The Physical Agent Harness——harness 边界延伸到物理层（2026-09-16 补充）

**来源**：[rashidazarang.com - The Physical Agent Harness](https://rashidazarang.com/c/the-physical-agent-harness)（2026-09-14，HN 2026-09-15 收录）

### 核心论点

- 一套便携视频装置最终演化为人类对话与 agent 系统之间的**硬件边界**——提出「物理 Agent Harness」概念：persistent agents 需要一个可携带的「身体接口」
- 把 harness engineering 的讨论从纯软件（context / loop / guardrails）**延伸到物理层**，是本周视角最新颖的一篇

### 与既有条目的关系

- 与 Latent Space「为人类注意力搭 harness」（09-15 补充）同一演进方向：模型自动化程度越高，人类侧接口（本条：物理设备；Latent Space：注意力治理）成为 harness 的新价值区
- 为「harness 是什么」的边界讨论补上常被忽略的一角：harness 不一定是软件

---

*更新时间：2026-09-16*
