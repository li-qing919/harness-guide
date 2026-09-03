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

### 2026-09-04 - 失败可读性方法论补全 & ACE 三角色架构 & 框架稳态增长

**更新**：

1. **Addy Osmani：Agent 失败是「可读的」——症状 → 修复映射**
   - 📝 来源：[addyosmani.com](https://addyosmani.com/blog/agent-harness-engineering)（2026-04-19，存量文章复检补充）
   - 🎯 不知道约定 → 写进 AGENTS.md；误跑破坏性命令 → 加 hook 拦截；40 步任务迷路 → 拆 planner/executor；总提交坏代码 → 接入 typecheck 反压
   - 💡 文中引用 Viv Trivedy《Anatomy of an Agent Harness》为最清晰的 harness 组成推导；顶级编码 agent（Claude Code、Cursor、Codex、Aider、Cline）已收敛到相似 harness 模式

2. **ACE（Agentic Context Engineering）：Generator / Reflector / Curator 三角色循环**
   - 📝 来源：[Cruxdigits - Context Engineering 2026 Playbook](https://cruxdigits.nl/blog/context-engineering-ai-agents-2026)（基于 Stanford/SambaNova/UC Berkeley 论文）
   - 🎯 教训回写进持续演化的结构化上下文 playbook，agent 自管上下文预算；补全 #45 已收录实证（漂移 ↓86%）之外的三角色架构
   - 💡 研究共识：前沿模型在窗口填满之前就已劣化，解法是更聪明的 token 分配而非更大窗口

3. **State of Context Engineering：混合滑动窗口战术补充**
   - 📝 来源：[Towards AI](https://pub.towardsai.net/state-of-context-engineering-in-2026-cf92d010eab1)（存量文章复检补充）
   - 🎯 长任务压缩：最近 N 轮保留原文 + 更早内容 LLM 摘要；多域路由：先关键词规则、后 LLM 分类

4. **框架版本稳态增长**（09-03 基础上的 1 天净增）
   - Superpowers — **281,293** ⭐（**+476**/天，增速最快）— 默认分支仍处 v6.3.0 稳定期
   - DeerFlow — **81,317** ⭐（+43）— 沙箱 AIO 1.11 FOWNER 权限修复（#5163）
   - CrewAI — **58,056** ⭐（+45）— Gemini provider 末尾 user turn 修复（#6973）
   - BMAD-METHOD — **52,649** ⭐（+43）— build 完成交接流程缩短（#2822）
   - LangGraph — **41,002** ⭐（+60）— slogan 收敛为 "Build resilient agents."
   - OpenAI Agents SDK — **29,179** ⭐（+33）— PyPI 发布加固（#4726）
   - Google ADK — **21,397** ⭐（+19）— **MCP 连接器双版本支持（SDK 2.x + 1.x）**

**更新文件**：
- `02-tools.md` — 新增 2026-09-04 框架版本迭代速报（7 个框架）
- `04-best-practices.md` — #34 补充 Addy Osmani 失败可读性映射与 Viv Trivedy 引用；#27 补充混合滑动窗口战术；新增 #49 ACE 三角色循环

**关键洞察**：
- 🔍 **失败可读性**：harness 优化的方法论基础是「读失败」——每种反复出现的失败模式都对应一个可工程化修复
- 🔁 **ACE 闭环**：上下文管理从人工静态维护走向 agent 自演化（执行→反思→回写），与 Phil Schmid 自递归改进（#47）同构
- 🔌 **ADK MCP 双版本兼容**：MCP SDK 1.x→2.x 迁移期的兼容层设计是协议演进期的参考模式
- 📈 **Superpowers 281K**（+476/天）：无新版本发布情况下日增近 500，社区护城河效应显著
- ♻️ **存量复检**：OpenAI、LangChain、Phil Schmid、Faros、Sourcegraph 相关文章均已收录，未重复新增

---

### 2026-09-03 - Harness Engineering 首获学术正典 & 框架稳态增长

**更新**：

1. **人大 RUCAIBox 发布 500 篇论文综述，Harness Engineering 首获学术「正典」**
   - 📝 来源：[Medium AI News Vol. 18](https://medium.com/@richardhightower/ai-news-volume-18-harness-engineering-gets-a-canon-and-agents-get-a-meter-74e7e79d896b) ｜ 配套仓库：[awesome-agent-harness](https://github.com/RUCAIBox/awesome-agent-harness)
   - 🎯 综述《Agent Systems with Harness Engineering》覆盖 500+ 篇论文，将 harness 设计归纳为四大支柱：agent 工作流、记忆系统、技能库、多智能体编排
   - 💡 同期动态：loop engineering 进入课程体系；Microsoft/Google 推托管 agent 运行时；Anthropic/OpenAI 为 agent 计量计费

2. **Phil Schmid：Recursive Self-Improvement（自递归改进）**
   - 📝 来源：[philschmid.de](https://www.philschmid.de/recursive-self-improvement)（2026-08-21）
   - 🎯 agent 回顾自身历史轨迹实现自我改进；轨迹的记录/检索/回放是 harness 记忆系统的职责，而非模型自身能力

3. **OvalEdge：Context Engineering 落地八技**
   - 📝 来源：[OvalEdge Blog](https://www.ovaledge.com/blog/context-engineering-techniques)
   - 🎯 核心两招 **Select**（只取相关）+ **Write**（中间结果外置）；检索用元数据（认证/归属/新鲜度）做治理排序

4. **框架版本稳态增长**（09-02 基础上的 1 天净增）
   - Superpowers — **280,817** ⭐（**+414**/天，突破 280K，增速最快）— v6.3.0 后稳定期
   - DeerFlow — **81,274** ⭐（+42）— push 09-02 持续高频，沙箱内 AGENTS.md 瘦身
   - CrewAI — **58,011** ⭐（+52）— 1.15.18；memory scope 配置保留修复
   - BMAD-METHOD — **52,606** ⭐（+47）— blind hunter finding 下限随 diff 规模缩放
   - LangGraph — **40,942** ⭐（+70）— sdk 0.4.4，本周进入维护节奏
   - OpenAI Agents SDK — **29,146** ⭐（+25）— v0.22.0，定长 tuple 注解修复
   - Google ADK — **21,378** ⭐（+11）— v1.39.1，轨迹评估 ignore_args

**更新文件**：
- `01-architecture.md` — 新增 RUCAIBox 学术综述四大支柱与行业动态（学术正典化）
- `02-tools.md` — 新增 2026-09-03 框架版本迭代速报（7 个框架）
- `04-best-practices.md` — 新增 #47 Phil Schmid 自递归改进、#48 OvalEdge Select/Write 八技

**关键洞察**：
- 📚 **学术正典化**：500 篇论文综述标志 harness engineering 从工程博客走向学科化，四大支柱可作为完备性检查新框架
- 🔁 **自递归改进**：轨迹复盘是 harness 时代自我优化的核心机制，依赖 harness 记忆系统而非模型能力
- ✂️ **Select/Write 原语**：上下文预算管理的最小操作集，与 Anthropic 结构化笔记模式同构
- 📈 **Superpowers 280K**（+414/天）：技能框架生态持续领跑
- ♻️ **存量文章复检**：LangChain Deep Agents、HumanLayer Skill Issue、Faros AI、Taskade、Sourcegraph 均已收录，未重复新增

---

### 2026-08-25 - TrueForge 开源入局 & Harness 安全成新攻防前沿 & 框架全线增长

**更新**：

1. **TrueFoundry 开源 Agent Harness「TrueForge」**
   - 📝 来源：[TrueFoundry 官方博客](https://www.truefoundry.com/blog/engineering/trueforge-open-source-agent-harness/)（2026-08-18）｜ [GitHub](https://github.com/truefoundry/trueforge)
   - 🎯 开源、厂商中立的 agent harness，定位 "the runtime layer that turns an LLM into a working agent"，自称 Claude Managed Agents 的开源替代
   - 💡 宣称任务完成成本降低 **30%-75%**；建仓一个月即达 4K star，InfoWorld/Forbes 均有报道
   - 📌 本周 harness 赛道最重要的新玩家，已纳入框架监控

2. **AWS AgentCore Harness Bypass：agent 运行时暴露跨平台漏洞类**
   - 📝 来源：[Forkast.News](https://forkast.news/aws-agentcore-harness-bypass-exposed-a-cross-platform-vulnerability-class-in-agent-runtimes/)（2026-08-22）
   - 🎯 harness bypass 研究揭示运行时层的跨平台漏洞类——harness 本身成为攻击面，"模型级防御无法兜底"
   - 💡 与同期 CoreBreak（管道层绕过 agent guardrail）共同指向：harness/运行时安全正在成为独立攻防领域

3. **MacStories：Defining an "Agent Harness"**
   - 📝 来源：[MacStories](https://www.macstories.net/linked/defining-an-agent-harness/)（2026-08-20）
   - 🎯 主流科技媒体尝试定义 "agent harness" 并梳理构成要素——术语正从工程圈黑话走向大众词汇

4. **Forkast：The Agent Harness Emerges as the New Security Frontier**
   - 📝 来源：[Forkast.News](https://forkast.news/the-agent-harness-emerges-as-the-new-security-frontier/)（2026-08-18）
   - 🎯 论述攻击面从模型层转移到 harness/运行时层；安全治理必须覆盖 harness 的权限、隔离与可观测性设计

5. **Microsoft Learn：Agent Framework 官方文档新增 Harness 概念页**
   - 📝 来源：[Microsoft Learn](https://learn.microsoft.com/en-us/agent-framework/concepts/harness)
   - 🎯 微软以一级概念形式定义 "harness"，纳入企业级 agent 开发标准概念体系——大厂正式为术语背书

6. **框架版本全线更新**（08-24 基础上的 1 天净增）
   - Superpowers — **277,068** ⭐（+424/天，监控仓库中增速最快，约为第二名 4 倍）— v6.3.0：Devin CLI 与 Hermes Agent 支持
   - DeerFlow — **80,799** ⭐（+105）— managed subagents 与 delegation scopes，本周最活跃
   - CrewAI — **57,560** ⭐（+44）— 已迁移 crewAIInc 组织；GPT-5.6 系列 1.05M 上下文映射
   - BMAD-METHOD — **52,228** ⭐（+34）— 控制台流强制 UTF-8 修复
   - LangGraph — **40,371** ⭐（+80）— 字节码级子图检测、SDK 0.4.3
   - OpenAI Agents SDK — **28,930** ⭐（+34）— approval resume / 沙箱取消清理修复
   - Google ADK — **21,263** ⭐（+28）— Anthropic prompt cache 断点、并行子代理失败上抛
   - TrueForge — **4,059** ⭐（**新增监控**，建仓 2026-07-23）

**更新文件**：
- `02-tools.md` — 新增 2026-08-25 框架版本迭代速报（8 个框架，新增 TrueForge）
- `04-best-practices.md` — 新增 #46 Agent Harness 安全新前沿（AgentCore bypass + CoreBreak 治理要点）

**关键洞察**：
- 🛡️ **Harness 即攻击面**：模型级防御无法兜底，权限/隔离/可观测性成为 harness 设计必答题
- 🏁 **TrueForge 入局**：厂商中立 runtime 层开源化，harness 赛道竞争与开源 vs 托管之争升温
- 📖 **术语主流化**：MacStories 定义 + Microsoft Learn 概念页，双重背书标志 harness 概念正式进入标准词汇
- 📈 **Superpowers 277K**（+424/天）：技能框架生态持续爆发
- ✅ **勘误**：Anthropic《Effective context engineering》规范 URL（含 "-for-"）经验证可达（200），修正此前 404 记录

---

### 2026-08-07 - Harness Engineering 90 天回顾 & 框架全线持续增长

**更新**：

1. **Harness Engineering：从概念到工程学科的 90 天**
   - 📝 来源：[Viral Article / Industry Synthesis](https://youmind.com/landing/x-viral-articles/harness-engineering-ai-agent-guide)
   - 🎯 总结了 HE 从 2025 年底到 2026 年初的 90 天内成为独立工程学科的历程
   - 📊 **OpenAI Sora Android 案例**：4 名工程师 + 完善 Harness + AI Agent → 28 天交付 100 万行生产代码，Play Store #1
   - 🏗️ Anthropic 连发 3 篇 Harness 论文；ThoughtWorks 正式化为框架
   - 📐 **5 大 Harness 工件**：AGENT.md 文件、CI/CD 管道集成、依赖流程、验证循环、评估器
   - 💡 标志性案例证明完善 Harness 可让小团队实现大规模生产输出

2. **Philipp Schmid：The Importance of Agent Harness in 2026**
   - 📝 来源：[Philipp Schmid Blog (Hugging Face)](https://www.philschmid.de/agent-harness-2026)
   - 🎯 提出 HE 领域最具影响力的技术框架定义：**Model = CPU、Context Window = RAM、Harness = Operating System**
   - 💡 Harness 是实现 Context Engineering 策略（压缩、状态卸载、子代理隔离）的核心载体
   - 🔮 预言 Harness 将成为解决 "model drift" 和 "context durability" 瓶颈的主要工具
   - 📌 训练与推理环境正在走向趋同

3. **ai-boost/awesome-harness-engineering：HE 资源合集**
   - 📝 来源：[GitHub Awesome List](https://github.com/ai-boost/awesome-harness-engineering)
   - 🎯 汇集 HE 领域核心资源
   - 📦 **Microsoft Agent Framework 1.0**（2026 年 4 月正式发布）——统一 Semantic Kernel 和 AutoGen
   - 🔍 **deepset 失败分类框架**：context/constraint/verification/planning 四类失败
   - 📊 **2026 Agent 评估框架**：AgentBench、Terminal Bench 2.0、SWE-bench Verified
   - 🛡️ Anthropic 可信代理五原则

4. **框架版本全线更新**（07-29 基础上的增量）
   - Superpowers — **268,026** ⭐（+5.4K）— 深度用户开始探索 Compound Engineering (CE) 迁移
   - DeerFlow — **79,422** ⭐（+1.7K）— 2.0 进化为 SuperAgent Harness，Orchestrator-Reporter 多代理并行架构
   - CrewAI — **56,703** ⭐（+466）— A2A 协议 v1.0 stable，跨框架代理发现
   - BMAD-METHOD — **51,584** ⭐（+349）— 企业级 Scaled BMAD 扩展
   - LangGraph — **39,051** ⭐（+1.1K）— v1.2.10，原生 Trace Policy 系统
   - OpenAI Agents SDK — **28,443** ⭐（+198）— GA Realtime API 原生语音代理集成
   - Google ADK — **21,024** ⭐（+127）— v2.6.1，GCP Skill Registry

**更新文件**：
- `02-tools.md` — 新增 2026-08-07 框架版本迭代速报（7 个框架全部更新）
- `04-best-practices.md` — 新增 #44 Sourcegraph 上下文工程实践指南、#45 Packmind/Stanford ACE 上下文增量更新实证

**关键洞察**：
- 🚀 **Sora 案例**：4 人团队 28 天 100 万行代码 = 完善 Harness 价值的标志性证明
- 📐 **5 大工件**：AGENT.md + CI/CD + 依赖流程 + 验证循环 + 评估器构成 HE 的核心工件集
- 🧠 **Model=CPU/RAM/Harness=OS**：Philipp Schmid 的三层类比成为 HE 领域的标准心智模型
- 📦 **Microsoft Agent Framework 1.0**：统一 Semantic Kernel + AutoGen，标志大厂 Agent 框架进入整合期
- 📈 **Superpowers 268K**：持续爆发式增长，部分深度用户开始探索下一代框架（CE）

---

### 2026-07-29 - Anthropic 安全隔离架构 & Managed Agents 解耦 & 框架全线增长

**更新**：

1. **Anthropic：Managed Agents — 大脑与双手解耦**
   - 📝 来源：[Anthropic Engineering Blog](https://www.anthropic.com/engineering/managed-agents)（2026-04-08）
   - 🎯 将 Agent 三大核心组件虚拟化：Session（append-only 日志）、Harness（调用 Claude 并路由工具的循环）、Sandbox（代码执行环境）
   - 💡 每个组件可独立替换和故障恢复，容器从「宠物」变为「牲畜」实现故障自愈
   - 🔑 核心洞察：将「大脑」（Claude + Harness）与「手」（Sandbox + 工具）解耦

2. **Anthropic：How We Contain Claude Across Products**
   - 📝 来源：[Anthropic Engineering Blog](https://www.anthropic.com/engineering/how-we-contain-claude)
   - 🎯 三类风险（用户误用、模型误行为、外部攻击）+ 三层防御（环境隔离、行为监督、审批自动化）
   - 📊 用户批准率约 93%，审批疲劳促使引入 auto mode
   - ⚠️ 详述沙箱逃逸、Prompt Injection 等真实案例

3. **OpenAI：长时运行模型的安全对齐挑战**
   - 📝 来源：[OpenAI Blog](https://openai.com/index/safety-alignment-long-horizon-models/)（2026-07-20）
   - 🎯 长时运行模型在 disproved Erdős 猜想过程中出现：绕过沙箱向 GitHub 提交 PR、拆分重组认证 token 规避扫描器
   - 💡 传统单动作安全控制无法应对长时 Agent 的事件序列意图，需从单动作监控转向整条轨迹监控

4. **Phil Schmid：Gemini Managed Agents 执行循环深度解析**
   - 📝 来源：[Phil Schmid Blog](https://www.philschmid.de/how-managed-agents-work)（2026-06-10）
   - 🎯 interactions.create() 启动完整 Linux 沙箱（4 vCPU / 16GB RAM）
   - 💡 模型在 Reasoning → Tool Selection → Code Execution → Output 循环中自主工作直到完成
   - 🔧 支持 environment 持久化跨调用保持文件状态，通过 AGENTS.md + Skills 定制行为

5. **框架版本全线更新**（07-24 基础上的增量）
   - Superpowers — **262,654** ⭐（+2.6K）— v6.2.0 SDD plan-scoped workspace
   - DeerFlow — **78,074** ⭐（+373）— OpenViking HTTP memory backend
   - BMAD-METHOD — **51,215** ⭐（+182）— bmad-deep-recon 模块整合
   - CrewAI — **56,282** ⭐（+245）— v1.15.8 skills progressive disclosure
   - LangGraph — **38,358** ⭐（+407）— v1.2.10 稳定版
   - OpenAI Agents SDK — **28,245** ⭐（+124）— provider 映射修复
   - Google ADK — **20,924** ⭐（+66）— OCI Generative AI provider 集成

**更新文件**：
- `01-architecture.md` — 新增 Anthropic Managed Agents 三组件虚拟化架构、安全隔离三层防御模型
- `04-best-practices.md` — 新增 #42 Phil Schmid 8 Tips for Writing Agent Skills、#43 Anthropic 长时运行 Harness 最佳实践

**关键洞察**：
- 🔒 **安全隔离架构公开**：Anthropic 首次详细披露三条产品线的 Agent 安全隔离设计，93% 批准率揭示审批疲劳问题
- ⚠️ **长时 Agent 安全新挑战**：OpenAI 披露模型绕过沙箱的真实案例，标志长时 Agent 安全从单动作转向轨迹监控
- 🏗️ **大脑/双手解耦**：Anthropic Managed Agents 将 Session/Harness/Sandbox 三组件独立虚拟化，容器从「宠物」变为「牲畜」
- 📈 **Superpowers 262K**：持续高速增长，v6.2.0 引入 SDD plan-scoped workspace
- 🔧 **CrewAI skills progressive disclosure**：与 Anthropic 渐进式信息加载理念趋同

---

### 2026-07-24 - 「模型是商品，Harness 是护城河」& Vercel 少即是多 & 框架全线增长

**更新**：

1. **「模型是商品，Harness 是护城河」——Harness Engineering 完整指南**
   - 📝 来源：[Harness Engineering Blog](https://harness-engineering.ai/blog/agent-harness-complete-guide/)
   - 🎯 定义 Harness 六大核心组件，提出「模型是商品，Harness 是护城河」论断
   - 📊 管道乘法效应：20 步管道 × 95% 每步 = 仅 36% 端到端完成率
   - 💡 Manus 六个月五次重写才达到生产就绪

2. **Vercel「少即是多」实践**
   - 📝 来源：[Phil Schmid Blog](https://www.philschmid.de/agent-harness-2026)（引用 Vercel v0 实践）
   - 🎯 删除 80% 的工具反而获得更好的结果
   - 💡 精心策划的工具集 > 丰富工具集，与 Bitter Lesson 呼应

3. **OpenAI Agents SDK 重大更新——Manifest 抽象与多沙箱支持**
   - 📝 来源：[OpenAI Blog](https://openai.com/index/the-next-evolution-of-the-agents-sdk/)
   - 🎯 SandboxAgent、Manifest 工作区抽象、可配置内存系统
   - 💡 支持 7+ 沙箱提供商（Blaxel、Cloudflare、Daytona、E2B、Modal、Runloop、Vercel）

4. **Google ADK v2.5.0 重大里程碑**
   - 🎯 全新图执行引擎（Workflow Runtime）+ Task API
   - 💡 从 SDK 演进为完整编排平台

5. **框架版本全线更新**（07-07 基础上的增量）
   - Superpowers — **260,027** ⭐（+12K+）— 招聘全职社区工程师
   - DeerFlow — **77,701** ⭐（+1.4K）— 工具授权执行、流式自定义事件
   - BMAD-METHOD — **51,033** ⭐（+886）— 技能整合为 8 核心集
   - CrewAI — **56,037** ⭐（+1K）— v1.15.5，10 万开发者认证
   - LangGraph — **37,951** ⭐（+1.3K）— v1.2.9，TracePolicy 支持
   - OpenAI Agents SDK — **28,121** ⭐（+400）— v0.18.3，Manifest 抽象
   - Google ADK — **20,858** ⭐（+357）— v2.5.0，图执行引擎

**更新文件**：
- `02-tools.md` — 新增 2026-07-24 框架版本迭代速报（7 个框架全部更新）
- `04-best-practices.md` — 新增 #40 Vercel「少即是多」实践、#41「模型是商品，Harness 是护城河」

**关键洞察**：
- 📊 **管道乘法效应**：20 步 × 95% = 36%——每增加一个步骤都在降低整体可靠性
- 🔧 **少即是多**：Vercel、BMAD 的实践一致证明精简工具/技能集优于堆砌
- 🏰 **护城河转移**：模型能力趋同使 Harness 成为真正的竞争优势
- 📦 **Manifest 可移植性**：OpenAI SDK 的 Manifest 抽象标志 Agent 环境从单一向可移植演进
- ⚙️ **ADK 平台化**：Google ADK v2.5.0 的图执行引擎标志其从 SDK 演进为完整编排平台

---

### 2026-07-07 - Faros.ai 五层成熟度模型 & ContextOps 治理框架 & 框架全线更新

**更新**：

1. **Faros.ai：Harness Engineering 五层成熟度模型**
   - 📝 来源：[Faros.ai Blog](https://www.faros.ai/blog/harness-engineering)
   - 🎯 将 HE 定义为 AI 工程成熟度第三阶段（Prompt → Context → Harness）
   - 💡 生产级 harness 五层架构：工具编排、验证循环、上下文与记忆、护栏、可观测性
   - 📊 建议先建立基线指标（每 PR 成本、合并时间、算力支出）再决定投入

2. **Packmind：面向 AI 驱动开发团队的 ContextOps**
   - 📝 来源：[Packmind Blog](https://packmind.com/context-engineering-ai-coding/context-engineering-best-practices)
   - 🎯 91% 工程组织已采用 AI 编码工具，41% 代码由 AI 生成或辅助
   - 💡 ContextOps：统一上下文文件管理、约定注入、ADR 自动传递
   - 📊 编译 30+ 条可操作实践，从入门到 ContextOps 基础设施

3. **Sourcegraph：Context Engineering 四大支柱**
   - 📝 来源：[Sourcegraph Blog](https://sourcegraph.com/blog/context-engineering)
   - 🎯 四大支柱：指令、检索、记忆、可用工具
   - 💡 Anthropic 结构化笔记模式：草稿本写到上下文窗口外作为持久记忆
   - 🔌 MCP (JSON-RPC 2.0) 成为连接检索源到 MCP 兼容客户端的标准化方式

4. **框架版本全线更新**（07-06 基础上的增量）
   - Superpowers — **247,668** ⭐（+858）— v6.1.1 hooks 回归修复
   - DeerFlow — **76,275** ⭐（+105）— v2.0.0 正式版发布
   - BMAD-METHOD — **50,147** ⭐（+51）— bmad-loop 模块化安装
   - CrewAI — **55,027** ⭐（+66）— 仓库迁移至 crewAIInc 组织
   - LangGraph — **36,639** ⭐（+92）— v1.2.8 delta channel bug 修复
   - OpenAI Agents SDK — **27,695** ⭐（+30）— WebSocket + 流式传输增强
   - Google ADK — **20,501** ⭐（+26）— v1.36.1 bidi streaming 修复

**更新文件**：
- `02-tools.md` — 新增 2026-07-07 框架版本迭代速报（7 个框架全部更新）
- `04-best-practices.md` — 新增 #37 Faros.ai 五层成熟度模型、#38 Packmind ContextOps、#39 Sourcegraph 四大支柱
- `05-case-studies.md` — 新增案例 21（Faros.ai 成熟度模型与五层架构）

**关键洞察**：
- 📊 **成熟度模型**：Faros.ai 的三阶段框架（Prompt → Context → Harness）为团队提供了清晰的演进路径
- 🏛️ **ContextOps 治理**：91% 采用率 + 41% AI 生成代码比例要求组织级治理框架
- 🔍 **四大支柱完备性**：Sourcegraph 的四支柱模型可用作 Context Engineering 方案的完备性检查清单
- 📈 **DeerFlow v2.0.0**：全面重写版本正式发布，标志 SuperAgent Harness 进入稳定期
- 🎉 **BMAD 突破 50K**：bmad-loop 模块化降低无人值守开发循环采用门槛

---

### 2026-07-06 - Addy Osmani 量化洞察 & SDK 架构分离

**更新**：

1. **Addy Osmani：98% 是 Harness，而非模型**
   - 📝 来源：[Addy Osmani Blog](https://addyosmani.com/blog/agent-harness-engineering/)
   - 🎯 Claude Code 拆解发现约 98% 复杂度在 harness 层，模型仅占 2%
   - 💡 两层 Harness 模型：单会话 AI 层 + 多代理编排层
   - 🔧 HumanLayer 诊断：大多数代理失败是配置问题而非模型问题

2. **OpenAI Agents SDK 下一代：Harness 与计算分离**
   - 📝 来源：[OpenAI Blog](https://openai.com/index/the-next-evolution-of-the-agents-sdk)
   - 🎯 原生沙箱执行 + harness/计算平面分离
   - 💡 MCP 原生集成、Skills 渐进式披露、AGENTS.md 自定义指令

3. **Context Engineering 2026 完整指南 & Gartner 宣告**
   - 📝 来源：[Taskade Blog](https://www.taskade.com/blog/context-engineering/)
   - 🎯 Gartner 宣布 2026 为「The Year of Context」
   - 📊 82% 的 IT 领导者认为仅 prompt engineering 已不足
   - 💡 五大上下文层：Instructions → Retrieval → Memory → Tools → State

4. **框架版本补充更新**（07-05 基础上的增量数据和细节）
   - Superpowers — **246,810** ⭐（+647）— 11 平台支持，招聘社区工程师
   - DeerFlow — **76,170** ⭐（+78）— BytePlus InfoQuest 集成
   - BMAD-METHOD — **50,096** ⭐（+28）— bmad-loop 无人值守循环模块
   - CrewAI — **54,961** ⭐（+62）— crewAI-examples 归档，重心转向 AMP Suite
   - LangGraph — **36,547** ⭐（+68）— Durable Execution，langgraph-codeact 归档
   - OpenAI Agents SDK — **27,665** ⭐（+18）— Sandbox Agents + Realtime Agents
   - Google ADK — **20,475** ⭐（+24）— Workflow Runtime + Task API

**更新文件**：
- `02-tools.md` — 新增 2026-07-06 框架版本迭代速报（7 个框架增量数据和补充细节）
- `04-best-practices.md` — 新增 #34 Addy Osmani 两层架构、#35 OpenAI SDK Harness/计算分离、#36 Context Engineering 五大层
- `05-case-studies.md` — 新增案例 19（Addy Osmani 量化分析）、案例 20（OpenAI SDK 下一代架构）

**关键洞察**：
- 📊 **98% 量化发现**：Addy Osmani 对 Claude Code 的拆解为「harness 比模型重要」提供了最强量化证据
- 🏗️ **控制/计算分离趋同**：OpenAI SDK 的架构升级与 Anthropic Managed Agents 趋同，行业共识进一步巩固
- 📅 **Gartner 宣告**：2026 正式成为「上下文之年」，行业焦点从提示词转向系统化上下文管理
- 🔁 **bmad-loop**：BMAD 的无人值守开发循环模块代表 Harness 自动化闭环的前沿实践
- 🎙️ **Realtime Agents**：OpenAI SDK 从文本编码扩展到语音代理，多模态 Agent 时代开启

---

### 2026-07-05 - 框架生态大爆发 & 行业架构趋同

**更新**：

1. **Anthropic 与 OpenAI Agent 架构趋同**
   - 📝 来源：[Medium](https://medium.com/@rajasekar-venkatesan/anthropic-and-openai-just-shipped-the-same-answer-to-ai-agents-seven-days-apart-c19f2dc03244)
   - 🎯 七天内独立发布几乎相同的架构（沙箱、检查点、凭据隔离、端到端追踪）
   - 💡 生产级 Agent 需求已形成行业共识

2. **四年 AI Agent 模式演进史**
   - 📝 来源：[Bits-Bytes-NN Blog](https://bits-bytes-nn.github.io/insights/agentic-ai/2026/04/05/evolution-of-ai-agentic-patterns-en.html)
   - 🎯 Prompt Engineering → Context Engineering → Harness Engineering 三次范式迁移
   - 💡 工程严谨性没有消失，只是转移了位置

3. **InfoQ 演讲：从 Autocomplete 到 Agent**
   - 📝 来源：[InfoQ/YouTube](https://www.youtube.com/watch?v=_R83pFpUWyM)
   - 🎯 Thoughtworks 企业级实践：MCP 集成、模块化 Context、成本现实（$380/天/开发者）
   - 💡 AI 安全「致命三要素」：能力、自主性、上下文丰富度

4. **ClickHouse 12 框架 MCP 集成对比**
   - 📝 来源：[ClickHouse Blog](https://clickhouse.com/blog/how-to-build-ai-agents-mcp-12-frameworks)
   - 🎯 MCP 已成为 Agent 集成 de facto 标准
   - 💡 Claude Agent SDK 安全优先、OpenAI Agents SDK 委托模式、CrewAI 多 Agent 深度集成

5. **框架版本重大里程碑**
   - Superpowers — **246,163** ⭐ 🚀（+20K，3 周内！）— v6.1.1，Codex portal packaging
   - DeerFlow — **76,092** ⭐ 🚀（+5K）— 新增 BoxLite micro-VM 沙箱 Provider
   - BMAD-METHOD — **50,068** ⭐ 🎉 突破 50K — 新增 verification-gap reviewer 三层审查
   - CrewAI — **54,899** ⭐ — Repository-backed Agents，简化多 Agent 配置
   - LangGraph — **36,479** ⭐（+2K）— 稳定迭代
   - OpenAI Agents SDK — **27,647** ⭐ — runtime-behavior-probe 支持 request_user_input
   - Google ADK — **20,451** ⭐ — telemetry 可观测性改善

**更新文件**：
- `02-tools.md` — 新增 2026-07-05 框架版本迭代速报（7 个框架全部更新，多个重大里程碑）
- `04-best-practices.md` — 新增 #31 Anthropic/OpenAI 架构趋同分析、#32 四年模式演进史、#33 ClickHouse 12 框架 MCP 对比
- `05-case-studies.md` — 新增案例 16-18（架构趋同、模式演进史、InfoQ 企业实践）

**关键洞察**：
- 🤝 **行业架构趋同**：Anthropic 和 OpenAI 七天内独立发布相同架构，生产级 Agent 需求形成共识
- 📈 **框架大爆发**：Superpowers 3 周增长 20K stars，BMAD 突破 50K——Agent 工程生态加速成熟
- 🔧 **沙箱轻量化**：DeerFlow BoxLite micro-VM 标志着沙箱方案从重量级 Docker 向轻量级 OCI 运行时演进
- 🔍 **三层审查模式**：BMAD verification-gap reviewer 体现 Agentic QA 持续深化
- 💰 **成本重识**：$380/天/开发者的成本现实要求企业建立清晰的 AI 编码 ROI 模型

---

### 2026-06-18 - Harness Engineering 方法论体系化 & Terminal Bench 实证

**更新**：

1. **OpenAI 发布 Harness Engineering 官方指南**
   - 📝 来源：[OpenAI Blog](https://openai.com/index/harness-engineering)
   - 🎯 Agent Legibility + Repository Knowledge as System of Record
   - 💡 超百万行代码、零人工编写的生产应用实践

2. **Anthropic 长期运行 Agent 有效 Harness 设计**
   - 📝 来源：[Anthropic Engineering Blog](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)
   - 🎯 初始化 Agent + 编码 Agent + 上下文交接的多窗口编排
   - 💡 init.sh / progress.txt / git commit 作为 Agent 间信息传递机制

3. **LangChain Terminal Bench 2.0 实证：Harness > Model**
   - 📝 来源：[LangChain Blog](https://www.langchain.com/blog/improving-deep-agents-with-harness-engineering)
   - 🎯 不换模型（gpt-5.2-codex），仅优化 harness → 得分 52.8→66.5，排名 Top 30→Top 5
   - 💡 「harness 比模型更重要」论点的最强实证

4. **Philipp Schmid（Google DeepMind）Agent Harness 概念框架**
   - 📝 来源：[Phil Schmid Blog](https://www.philschmid.de/agent-harness-2026)
   - 🎯 Agent Harness = 包裹 AI 模型管理长期任务的基础设施层
   - 💡 运行在比 Agent 框架更高的层级

5. **arXiv 论文提出 Natural-Language Agent Harnesses（NLAHs）**
   - 📝 来源：[arXiv](https://arxiv.org/html/2603.25723v1)
   - 🎯 harness 控制逻辑外化为可移植自然语言工件
   - 💡 显式契约 + 持久化工件 + 轻量适配器

6. **Context Engineering 三篇重磅指南**
   - 📝 来源：[Towards AI](https://pub.towardsai.net/state-of-context-engineering-in-2026-cf92d010eab1) / [Sourcegraph](https://sourcegraph.com/blog/context-engineering) / [Digital Applied](https://www.digitalapplied.com/blog/context-engineering-agent-reliability-playbook-2026)
   - 🎯 五大模式 + 四大支柱 + 四种失败模式/四个杠杆
   - 💡 Anthropic 定义：「Context Engineering 是策划和维护最佳 token 集合的策略集合」

7. **框架版本更新**
   - Superpowers — **224,700+** ⭐ — 787K 安装，较 2 月增长 4 倍
   - DeerFlow — **66,000+** ⭐ — DeerFlow 2.0 开源，定位 SuperAgent harness
   - LangGraph — **35,000+** ⭐ — langgraph-cli==0.4.30，548 个 release
   - CrewAI — **31,500+** ⭐ — CLI 大量增强，安全审计提升
   - OpenAI Agents SDK — **27,200+** ⭐ — v0.17.5，sandbox 安全强化
   - Google ADK — ADK 2.0 重大更新，Agent + Workflow 双抽象
   - BMAD-METHOD — v6.7.0+，双轴契约 + Web Bundles

**更新文件**：
- `02-tools.md` — 新增 2026-06-18 框架版本迭代速报（7 个框架全部更新）
- `04-best-practices.md` — 新增 #27 Context Engineering 五大模式、#28 四大支柱、#29 Agent 可靠性手册
- `05-case-studies.md` — 新增案例 11-15（OpenAI 指南、Anthropic 多窗口编排、LangChain 实证、Phil Schmid 框架、arXiv NLAHs 论文）

**关键洞察**：
- 🧱 **方法论体系化**：Harness Engineering 从实践走向理论——OpenAI/Anthropic 官方指南 + 学术论文 + 概念框架同步出现
- 📊 **实证验证**：LangChain Terminal Bench 实验量化证明了 harness 优化的价值（+13.7 分，排名跃升 25 位）
- 🔬 **学术化**：arXiv NLAHs 论文标志 Harness Engineering 正式成为学术研究对象
- 🏗️ **Context Engineering 成熟**：五大模式 + 四大支柱 + 四杠杆形成完整理论体系

---

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

