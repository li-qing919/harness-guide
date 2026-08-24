# 最佳实践

## 1. Neil Kakkar 的工作流

**来源**：[How I'm Productive with Claude Code](https://neilkakkar.com/productive-with-claude-code.html)

### 核心原则

> "I'm not the implementer anymore. I'm the manager of agents doing the implementation."
> 
> "The highest-leverage work I've done at Tano hasn't been writing features. It's been building the infrastructure that turned a trickle of commits into a flood."

### 四个阶段

#### 1️⃣ 自动化 PR 流程

**问题**：手动 PR 繁琐，打断心流

**解决方案**：创建 `/git-pr` skill

**效果**：
- 节省时间
- PR 描述更详细
- 移除心理负担

#### 2️⃣ 消除等待

**问题**：1 分钟重启时间，长到打断专注

**解决方案**：切换到 SWC

**效果**：
- 重启时间降到 < 1 秒
- 永远不离开心流
- 像流畅的对话

#### 3️⃣ 让 AI 自己验证

**问题**：人工验证 UI 成为瓶颈

**解决方案**：使用 Preview 功能

**规则**：
> "A change isn't 'done' until the agent has verified the UI itself."

**效果**：
- Agent 能运行更长时间
- 自己发现并修复错误
- 人工只做最终 review

#### 4️⃣ 并行一切

**问题**：只能同时处理一个任务

**解决方案**：Worktree + 端口隔离

**效果**：
- 从 2 个分支就崩溃 → 同时运行 5 个 worktree
- 每个功能独立环境
- Review 变得简单

### 循环理论

> "Each of these stages removed a different kind of friction. And each time I removed one, the next became visible. Classic theory of constraints — fix one, and the system immediately shows you the next one."

**摩擦类型**：
1. 格式化摩擦（PR 流程）
2. 等待摩擦（编译时间）
3. 验证摩擦（UI 检查）
4. 上下文切换摩擦（并行开发）

---

## 2. CLAUDE.md 配置规则

### 必备规则

```markdown
# 项目规则

## 1. UI 验证规则

任何前端代码变更，必须：
1. 启动 dev server
2. 使用 Preview 功能访问页面
3. 截图确认 UI 符合预期
4. 测试交互功能

禁止声明"完成"而未经 UI 验证。

## 2. Git 提交规则

遵循 Conventional Commits：
- `feat:` 新功能
- `fix:` Bug 修复
- `refactor:` 重构
- `docs:` 文档
- `test:` 测试
- `chore:` 杂项

## 3. 测试规则

1. 所有新功能必须有单元测试
2. 核心流程必须有 E2E 测试
3. 测试覆盖率 ≥ 80%

## 4. 代码规范

1. 使用 TypeScript
2. 使用 ESLint + Prettier
3. 单个函数不超过 50 行
4. 单个文件不超过 300 行
```

---

## 3. Agent Skills 设计

### 原则

1. **单一职责**：每个 skill 只做一件事
2. **可组合**：多个 skill 可以组合使用
3. **幂等性**：重复执行结果相同
4. **可回滚**：失败时可以回滚

### 示例 Skills

#### `/git-pr`

```markdown
---
name: git-pr
description: 自动化 PR 创建流程
---

## 工作流

1. 分析 git diff
2. 生成 commit message
3. 生成 PR description
4. 提交并创建 PR

## 使用

/git-pr
```

#### `/test-ui`

```markdown
---
name: test-ui
description: 自动化 UI 测试
---

## 工作流

1. 启动 dev server
2. 运行 Playwright 测试
3. 截图对比
4. 生成报告

## 使用

/test-ui --url http://localhost:3000
```

#### `/review-pr`

```markdown
---
name: review-pr
description: 自动化 PR 审查
---

## 工作流

1. 获取 PR diff
2. 分析代码质量
3. 检查安全漏洞
4. 生成审查意见

## 使用

/review-pr --pr 123
```

---

## 4. 性能优化

### 编译优化

| 工具 | 场景 | 速度提升 |
|------|------|---------|
| SWC | React/Next.js | 20-70x |
| esbuild | Vue/Vite | 默认已优化 |
| Turbopack | Next.js 13+ | 10x |

### 开发服务器优化

```javascript
// vite.config.js
export default {
  server: {
    // 启用 HMR
    hmr: true,
    // 预构建依赖
    optimizeDeps: {
      include: ['vue', 'axios']
    }
  }
}
```

### Agent 并行优化

```yaml
# openclaw 配置
agents:
  maxConcurrent: 5
  resourceLimits:
    cpu: 2
    memory: 4GB
```

---

## 5. 错误处理

### 自动重试

```javascript
// 重试逻辑
async function withRetry(fn, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (i === maxRetries - 1) throw error;
      await sleep(1000 * Math.pow(2, i)); // 指数退避
    }
  }
}
```

### 优雅降级

```javascript
// 降级逻辑
async function safeExecute(fn, fallback) {
  try {
    return await fn();
  } catch (error) {
    console.error('Execution failed:', error);
    return fallback;
  }
}
```

### 错误分类

| 类型 | 处理方式 |
|------|---------|
| 网络错误 | 自动重试 |
| 语法错误 | 人工介入 |
| 逻辑错误 | 回滚 + 人工审查 |
| 资源限制 | 降级运行 |

---

## 6. 安全最佳实践

### 敏感信息管理

**❌ 不要**：
- 在代码中硬编码密钥
- 在 `.env` 文件中提交密钥
- 在日志中打印敏感信息

**✅ 应该**：
- 使用环境变量
- 使用密钥管理服务
- 使用 `.env.example` 模板

### 权限控制

```yaml
# openclaw 配置
permissions:
  fileSystem: read-write-project-only
  network: restricted
  shell: whitelist-only
```

### 沙箱隔离

```yaml
# 沙箱配置
sandbox:
  enabled: true
  isolation: process
  resourceLimits:
    cpu: 2
    memory: 4GB
```

---

## 7. 监控和日志

### 关键指标

| 指标 | 目标值 | 监控方式 |
|------|--------|---------|
| Agent 执行时间 | < 5 分钟 | 自动记录 |
| Token 消耗 | < 10K/任务 | API 统计 |
| 成功率 | > 95% | 错误日志 |
| 人工介入率 | < 20% | 审计日志 |

### 日志级别

```
ERROR   - 需要人工介入的错误
WARN    - 可恢复的异常
INFO    - 正常操作日志
DEBUG   - 详细调试信息
```

### 日志格式

```json
{
  "timestamp": "2026-03-25T08:30:00Z",
  "level": "INFO",
  "agent": "main",
  "action": "file_edit",
  "file": "src/App.vue",
  "tokens": 1500,
  "duration": 1200
}
```

---

## 8. OpenAI Agent-First 开发原则（2026-03-26 更新）

**来源**：[OpenAI Blog - Harness Engineering](https://openai.com/index/harness-engineering/)

### 核心理念

> "Humans steer. Agents execute." - 人类掌舵，代理执行

### Context Engineering 黄金法则

> "Give Codex a map, not a 1,000-page instruction manual."

**AGENTS.md 应该是目录，而非百科全书**：

```
❌ 错误做法：
AGENTS.md (5000 行，包含所有规则)

✅ 正确做法：
AGENTS.md (~100 行) → 指向 docs/ 目录中的详细文档
    ├── docs/architecture.md
    ├── docs/quality.md
    ├── docs/plans/
    └── docs/decisions/
```

### 知识库结构

```
docs/
├── design/           # 设计文档（带验证状态）
├── architecture.md   # 架构地图
├── quality.md        # 各领域质量评分
├── plans/
│   ├── active/       # 进行中的计划
│   └── completed/    # 已完成的计划
└── decisions/        # 架构决策记录
```

### 机械强制

- CI 任务验证文档新鲜度
- 交叉链接检查
- "doc-gardening" Agent 自动修复过期文档

### 可观测性集成

**让 Agent 能直接访问日志和指标**：

```yaml
observability:
  logs:
    query_language: LogQL
    per_worktree: true  # 每个 worktree 独立
    
  metrics:
    query_language: PromQL
    targets:
      - "service startup < 800ms"
      - "critical journeys < 2s per span"
```

---

## 9. Anthropic 多 Agent 协作模式（2026-03-26 更新）

**来源**：[Anthropic Blog - Harness Design](https://www.anthropic.com/engineering/harness-design-long-running-apps)

### Generator-Evaluator 模式

```yaml
agents:
  generator:
    role: "实现代码和功能"
    prompt: |
      你是一个代码生成器。
      根据规划实现功能。
      生成后提交给评估器。
      
  evaluator:
    role: "评估和改进建议"
    prompt: |
      你是一个严格的代码评估器。
      使用以下标准评分：
      - 设计质量 (权重: 高)
      - 原创性 (权重: 高)
      - 技术执行 (权重: 中)
      - 功能性 (权重: 中)
      
      评分低于 8/10 时，返回改进建议。
```

### 自评偏差解决方案

**问题**：Agent 对自己的工作评价过高

**解决方案**：
1. 分离生成器和评估器
2. 评估器使用独立的提示词
3. 评估器被调优为"挑剔"

### 长时任务的 Harness 设计

```yaml
harness:
  # 任务分解
  decomposition:
    method: "initializer_agent"
    output: "task_list"
    
  # 上下文管理
  context:
    strategy: "reset"  # 而非 compaction
    handoff_artifact:
      - current_state
      - next_steps
      - decisions_made
      
  # 迭代循环
  iteration:
    max_loops: 10
    exit_condition: "evaluator_score >= 8"
```

---

## 10. Anthropic 长时任务 Harness 设计（2026-03-26 更新）

**来源**：[Anthropic - Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

### 双模式架构

```yaml
harness:
  # 首次运行
  initializer_agent:
    prompt: "specialized_first_run_prompt"
    outputs:
      - init.sh           # 环境初始化脚本
      - claude-progress.txt  # 进度日志
      - initial_commit    # 基线代码
      
  # 后续运行
  coding_agent:
    prompt: "incremental_progress_prompt"
    behavior: "每次会话只做增量进展"
    handoff: "留下清晰的 artifacts 给下一个会话"
```

### 进度文件结构

```markdown
# claude-progress.txt

## 2026-03-26 Session 1
- Set up project structure
- Created base components
- Next: Implement authentication

## 2026-03-26 Session 2
- Implemented login form
- Added JWT validation
- Next: Add permission system
```

### Claude 4 多上下文窗口最佳实践

> "Use a different prompt for the very first context window."

```yaml
prompts:
  first_window: |
    你是初始化 Agent。
    任务：设置项目环境
    输出：init.sh, claude-progress.txt, 初始提交
    
  subsequent_windows: |
    你是增量开发 Agent。
    任务：阅读 claude-progress.txt，继续开发
    规则：每次只做一个功能，完成后更新进度文件
```

---

## 11. 基准测试基础设施噪声（2026-03-26 更新）

**来源**：[Anthropic - Infrastructure Noise](https://www.anthropic.com/engineering/infrastructure-noise)

### 问题

> 基础设施配置可使基准测试波动几个百分点——有时甚至超过排行榜上顶级模型之间的差距。

### 推荐配置

```yaml
eval_infrastructure:
  # 不要只指定一个值
  resources:
    cpu:
      guaranteed: 2        # 保底分配
      limit: 6             # 硬杀阈值
    memory:
      guaranteed: 4GB
      limit: 12GB
      
  # 建议：3x ceiling
  ceiling_multiplier: 3    # 将错误率从 5.8% 降到 2.1%
```

### 排行榜解读

| 差距 | 可信度 |
|------|--------|
| < 3% | 需要质疑配置一致性 |
| 3-5% | 谨慎解读 |
| > 5% | 可能是真实差异 |

---

## 12. Agent Harness 的苦涩教训（2026-03-26 更新）

**来源**：[Phil Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)

### 核心价值

> A Harness turns vague, multi-step agent workflows into **structured data that we can log and grade**, allowing us to hill-climb effectively.

### Harness 作为测试平台

```yaml
harness_as_benchmark:
  purpose: "轻松测试和比较不同模型"
  
  capabilities:
    - 模型 A/B 测试
    - 性能对比
    - 约束验证
    
  benefits:
    - 可复现性
    - 可比较性
    - 可优化性
```

### 苦涩教训

> 构建复杂 Agent 系统的"苦涩教训"：简单的方法 + 更多计算 > 复杂的方法

**建议**：
- 优先投资基础设施
- 保持架构简单
- 让模型做繁重工作

---

## 13. Termdock 配置文件最佳实践（2026-03-26 更新）

**来源**：[Termdock Blog](https://termdock.com/zh/blog)

### SKILL.md vs CLAUDE.md vs AGENTS.md

| 文件 | 作用 | 谁读取 | 使用场景 |
|------|------|--------|---------|
| **SKILL.md** | 定义技能行为 | 特定 skill 调用时 | 可复用的技能模块 |
| **CLAUDE.md** | 项目上下文 | Claude Code 每次启动 | 项目级配置 |
| **AGENTS.md** | 工作区规则 | 所有 Agent 共享 | 全局规则 |

### 10 个让 AI Agent 变笨的 CLAUDE.md 错误

1. **过长**：超过 500 行
2. **结构混乱**：没有清晰的章节
3. **过时信息**：包含已删除功能的说明
4. **矛盾规则**：同一件事有多个不同的说明
5. **缺少示例**：只有抽象规则没有具体例子
6. **过度限制**：限制了 Agent 的创造力
7. **忽略上下文**：不考虑项目实际情况
8. **缺少验证**：没有告诉 Agent 如何验证
9. **硬编码路径**：路径写死而不是使用变量
10. **缺少更新机制**：没有说明如何更新文档

### 正确做法

```markdown
# CLAUDE.md 最佳实践

## 结构（控制在 100-200 行）

1. 项目概述（2-3 句）
2. 技术栈（列表）
3. 关键规则（5-10 条）
4. 常用命令（5-10 个）
5. 参考文档链接（指向详细文档）

## 规则示例

### ✅ 好的规则
- "所有 API 调用必须有错误处理"
- "测试文件与源文件同名，后缀 .test.ts"

### ❌ 坏的规则
- "代码要写好"（太模糊）
- "使用最佳实践"（没有具体说明）
```

---

## 14. Anthropic Context Engineering 官方最佳实践（2026-03-29 更新）

**来源**：[Anthropic - Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

### 全上下文状态管理

Anthropic 官方发布 Context Engineering 完整指南，覆盖五大上下文来源：

```
┌──────────────────────────────────────────────┐
│            Full Context State                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
│  │  System  │  │   Tool   │  │   MCP    │  │
│  │Instructions│  │Definitions│  │ Servers │  │
│  └──────────┘  └──────────┘  └──────────┘  │
│  ┌──────────┐  ┌──────────────────────────┐ │
│  │ External │  │    Message History       │ │
│  │   Data   │  │  (累积增长的主要来源)      │ │
│  └──────────┘  └──────────────────────────┘ │
└──────────────────────────────────────────────┘
```

### 关键策略

#### Tool Result Clearing（工具结果清除）
- Agent 执行工具后，大体积结果应及时清理
- 防止历史消息挤占 System Instructions 和工具定义空间

#### Context Compaction（上下文压缩）
- 定期压缩累积的对话历史
- 保留关键决策和状态信息
- 丢弃已处理的中间结果

#### Progressive Disclosure（渐进式信息加载）
- 按需加载，而非一次性注入全部上下文
- AGENTS.md → docs/ → 具体文件，层层递进

#### Memory Tool（记忆工具公测版）
- Claude Developer Platform 发布 Memory Tool 公测版
- 支持通过文件系统在上下文窗口外存储和查询信息
- 解决长任务中上下文窗口限制问题

---

## 15. Context Engineering 六大核心技术（2026-03-29 更新）

**来源**：[Context Engineering: The 6 Techniques That Actually Matter in 2026](https://pub.towardsai.net/context-engineering-the-6-techniques-that-actually-matter-in-2026-90bb0272ae85)

### 技术矩阵

| 技术 | 核心目标 | 实施方式 |
|------|---------|---------|
| **Progressive Disclosure** | 控制何时加载什么 | 分层加载：AGENTS.md → docs/ → 具体文件 |
| **Compression** | 压缩累积历史 | 摘要生成、关键信息提取、丢弃冗余 |
| **Routing** | 查询路由到正确来源 | 意图识别 → 选择正确的知识库/工具 |
| **Evolved Retrieval** | 进化式检索 | 向量检索 + 关键词混合、自适应排序 |
| **Tool Management** | 工具能力面控制 | 按需激活/隐藏工具、减少干扰 |
| **Evaluation** | 评估机制 | 自动评分、A/B 测试、反馈循环 |

### 生产环境组合策略

```
┌──────────────────────────────────────────────────┐
│                  Evaluation Layer                 │
│         (衡量整体效果，驱动优化循环)                 │
├──────────────────────────────────────────────────┤
│                 Retrieval Layer                   │
│        (按需加载外部知识和历史经验)                  │
├──────────────────────────────────────────────────┤
│            Runtime Management Layer               │
│     Routing + Compression 管理运行时上下文          │
├──────────────────────────────────────────────────┤
│              Entry Control Layer                  │
│    Progressive Disclosure + Tool Management       │
│          控制信息入口和工具可见性                     │
└──────────────────────────────────────────────────┘
```

### 实践建议

1. **分层组合使用**：单一技术不足以覆盖所有场景
2. **优先投入 Entry Control**：从 Progressive Disclosure 和 Tool Management 开始
3. **Evaluation 驱动优化**：建立量化指标，持续迭代
4. **避免过度设计**：按需添加，不是一次性实现全部

---

## 16. MCP 成为 Agent-Tool 连接标准（2026-03-29 更新）

**来源**：[State of Context Engineering in 2026](https://medium.com/@kushalbanda/state-of-context-engineering-in-2026-cf92d010eab1)

### MCP（Model Context Protocol）现状

- MCP 已成为连接 AI Agent 和外部工具的标准协议
- 被 Anthropic、OpenAI、Google 等主要 AI 厂商采纳
- 提供统一的工具发现、调用和结果返回机制

### 核心挑战：Action History 膨胀

```
上下文窗口分配：

理想状态：              实际情况：
┌───────────────┐      ┌───────────────┐
│ System Inst.  │ 30%  │ System Inst.  │ 10% ← 被挤压
├───────────────┤      ├───────────────┤
│ Tool Defs     │ 25%  │ Tool Defs     │ 8%  ← 被挤压
├───────────────┤      ├───────────────┤
│ Working Mem   │ 25%  │               │     │
├───────────────┤      │ Action History│ 82% ← 膨胀！
│ Action History│ 20%  │               │     │
└───────────────┘      └───────────────┘
```

### 推荐架构

```yaml
context_architecture:
  entry_control:
    - progressive_disclosure  # 渐进式披露
    - tool_management         # 工具面控制
    
  runtime_management:
    - routing                 # 查询路由
    - compression             # 历史压缩
    
  knowledge_layer:
    - retrieval               # 按需检索
    - external_data           # 外部数据源
    
  feedback_loop:
    - evaluation              # 效果评估
    - optimization            # 持续优化
```

---

## 17. Context Engineering 六层上下文架构（2026-03-30 更新）

**来源**：[Context Engineering for AI Agents (2025): Practical Guide](https://promptbuilder.cc/blog/context-engineering-agents-guide-2025)

### 六层结构模型

将 Agent 输入视为六层结构——从系统级到任务级：

```
┌──────────────────────────────────────────┐
│  Layer 6: Current Task (当前任务)         │
│  → 用户即时请求                           │
├──────────────────────────────────────────┤
│  Layer 5: Conversation History (对话历史) │
│  → 多轮对话上下文                         │
├──────────────────────────────────────────┤
│  Layer 4: Tool Definitions (工具定义)     │
│  → 可用工具和 API 描述                     │
├──────────────────────────────────────────┤
│  Layer 3: Retrieved Documents (检索文档)  │
│  → RAG / 知识库检索结果                    │
├──────────────────────────────────────────┤
│  Layer 2: Long-Term Memory (长期记忆)     │
│  → 持久化知识和偏好                        │
├──────────────────────────────────────────┤
│  Layer 1: System Instructions (系统指令)  │
│  → 角色、约束、全局规则                    │
└──────────────────────────────────────────┘
```

### 效果衡量指标

| 指标 | 说明 | 目标 |
|------|------|------|
| 任务成功率 | Agent 是否正确完成任务 | > 90% |
| 检索精度 | RAG 返回结果的相关性 | > 85% |
| 工具使用准确率 | 正确选择和调用工具 | > 95% |
| 用户满意度 | 用户对结果的满意程度 | > 80% |

---

## 18. Context Engineering 系统工程视角（2026-03-30 更新）

**来源**：[Redis Blog - Context Engineering Best Practices](https://redis.io/blog/context-engineering-best-practices-for-an-emerging-discipline/)

### 从 Prompt Engineering 到系统工程

> **Philipp Schmid**："构建有效 AI Agent 的秘诀不在于代码复杂度，而在于提供的上下文质量。"

> **Andrej Karpathy**："LLM 是新操作系统的内核进程。"

> **Lance Martin**："LLM 是 CPU，上下文窗口是 RAM。"

### Memory 层的关键地位

**核心论点**：没有记忆层的 Context Engineering 只是换了个名字的 Prompt Engineering。

```
Prompt Engineering:
  输入 → LLM → 输出（无状态）

Context Engineering:
  输入 + Memory + 检索 + 工具 → LLM → 输出（有状态）
                                         ↓
                                    更新 Memory
```

### 实践要点

1. **上下文质量 > 代码复杂度**：好的 Agent 不需要复杂的代码框架
2. **Memory 是分水岭**：区分 Context Engineering 和 Prompt Engineering 的关键组件
3. **RAM 类比**：上下文窗口有限，需要像管理内存一样管理上下文
4. **OS 类比**：LLM 如同操作系统内核，上下文工程如同系统编程

---

## 19. Context Engineering 防止"上下文腐烂"（2026-03-31 更新）

**来源**：[Context Engineering for AI Agents: 2026 Developer Guide (Fast.io)](https://fast.io/resources/context-engineering-ai-agents/)

### Context Rot（上下文腐烂）

> 对话过长导致模型丢失主要目标的问题——"context rot"是长时任务 Agent 最大的敌人。

### 实践建议

1. **预算化上下文窗口**：为不同类型内容分配固定的 token 预算比例
2. **压缩历史对话**：定期生成摘要，丢弃已处理的中间结果
3. **使用标准协议**（如 MCP）：确保工具上下文始终格式正确
4. **Task-Agent 模式**：选取最重要文件和关键对话轮次构建高密度上下文

### 核心洞察

> Context Engineering 不是单一技术，而是**防止信息退化的系统设计**。每一步操作都在消耗上下文预算，需要像管理财务预算一样管理上下文空间。

---

## 20. LangChain DeepAgents Harness Engineering 实战（2026-04-01 更新）

**来源**：[LangChain Blog - Improving Deep Agents with Harness Engineering](https://blog.langchain.com/improving-deep-agents-with-harness-engineering/)

### 核心成果

LangChain 团队通过聚焦三个优化维度，将 DeepAgents CLI 在 TerminalBench 2.0 上从 **52.8 提升到 66.5**（+13.7 分）：

### 三大优化维度

#### 1. System Prompt 优化
- 精心设计系统提示词，明确 Agent 角色和行为边界
- 结构化指令 > 自然语言描述

#### 2. Tools 优化
- 工具定义的精确性和完整性直接影响 Agent 表现
- 减少模糊工具，增加精确描述

#### 3. Middleware 优化（模型/工具调用钩子）
- 引入 **Ralph Wiggum Loop**：验证循环确保 Agent 不偏离任务
- **Multi-model Harness**：不同子任务使用不同模型

### Ralph Wiggum Loop 验证循环

```
Task → Agent Execute → Verify Output → [Pass → Next Task]
                                      → [Fail → Retry with Context]
```

核心思想：Agent 在完成每一步后进行自我验证，防止"看起来对但实际错"的结果累积。

### Multi-model Harness 概念

```yaml
harness:
  planning:
    model: "claude-opus"  # 高质量规划
    max_tokens: 4000
    
  coding:
    model: "gpt-4.1"     # 快速编码
    max_tokens: 8000
    
  review:
    model: "claude-sonnet"  # 精确审查
    max_tokens: 2000
```

### 实践建议

1. **优先优化 System Prompt**：投入产出比最高
2. **工具定义比数量重要**：精确定义 > 大量模糊工具
3. **引入验证循环**：防止错误累积
4. **考虑 Multi-model**：不同任务用不同模型

---

## 21. Context Engineering 可观测性实践（2026-04-01 更新）

**来源**：[Comet - Context Engineering: The Discipline Behind Reliable LLM Applications](https://www.comet.com/site/blog/context-engineering/)

### Context Engineering 的核心定义

> Context Engineering 是设计、治理和优化围绕每次 LLM 生成的全部信息——指令、事实、工具、策略——的学科。

### 访问和溯源管理

1. **所有权追踪**：维护每个上下文元素的所有权
2. **来源溯源**：记录检索数据的来源
3. **版本历史**：系统 prompt 的完整版本历史

### 可观测性工具推荐

使用 **Opik** 等工具捕获每次 LLM 调用：
- 系统 prompt 快照
- Few-shot 示例记录
- 检索上下文和元数据
- 完整的调用链追踪

```yaml
observability:
  tool: "opik"
  capture:
    - system_prompt
    - few_shot_examples
    - retrieval_context
    - metadata
    - call_chain
```

### 实践要点

1. **每次调用都是可审计的**：完整的上下文快照
2. **溯源即质量**：知道信息来自哪里比信息本身更重要
3. **版本化一切**：系统 prompt、few-shot 示例、检索策略都需要版本控制

---

## 22. LangChain Context Engineering 四大策略桶（2026-04-02 更新）

**来源**：[LangChain Blog - Context Engineering for Agents](https://blog.langchain.com/context-engineering-for-agents/)

### 四大策略框架

LangChain 将上下文工程系统化归纳为四个策略桶：

| 策略 | 核心操作 | 生产实践 |
|------|---------|---------|
| **Write** | 将信息持久化到上下文窗口外 | 长期记忆存储、知识库写入 |
| **Select** | 按需检索并注入上下文 | RAG 检索、向量查询、关键词匹配 |
| **Compress** | 清理工具结果、压缩历史 | Tool result clearing、摘要生成 |
| **Isolate** | 通过子 Agent 拆分上下文窗口 | 子 Agent 隔离执行、独立上下文 |

### 与 LangGraph 的结合

LangGraph 的长期记忆能力为 Write/Select 策略提供生产级支撑：

```yaml
context_engineering:
  write:
    tool: "langgraph_memory_store"
    strategy: "cross_session_persistence"
    
  select:
    tool: "langgraph_retrieval"
    strategy: "hybrid_vector_keyword"
    
  compress:
    tool: "langgraph_summarizer"
    strategy: "tool_result_clearing"
    
  isolate:
    tool: "langgraph_subgraph"
    strategy: "independent_context_window"
```

### 多产品实践案例

文章引用了多个主流 Agent 产品的实际实现，验证四大策略的通用性。

### 实践要点

1. **Write 是基础**：没有持久化，其他策略无法有效运作
2. **Select 需要精度**：检索相关性比检索数量更重要
3. **Compress 是日常**：每个工具调用后都应考虑清理
4. **Isolate 是终极手段**：当上下文窗口接近极限时使用

---

## 23. Context Engineering 2026 关键认知转变（2026-04-02 更新）

**来源**：[The AI Corner - Context Engineering Guide 2026: Prompt Engineering Is Dead](https://www.the-ai-corner.com/p/context-engineering-guide-2026)

### 三大认知颠覆

#### 1. "Think step by step" 对推理模型有害
- 推理模型（o1/o3、DeepSeek-R1）自带内部推理链
- 外部 CoT 提示反而干扰模型原生推理能力
- 📌 **启示**：根据模型类型选择提示策略，不要无脑使用 CoT

#### 2. 长 prompt 超过 3000 token 开始降低推理性能
- 上下文越长，模型"注意力"越分散
- 存在明确的性能拐点
- 📌 **启示**：精简 System Prompt，将详细文档外置到文件系统

#### 3. Few-shot CoT 仅剩格式对齐作用
- 推理模型不需要通过示例学习推理方式
- Few-shot 的价值退化为输出格式规范
- 📌 **启示**：用 1-2 个示例展示格式即可，不要用大量示例教模型"怎么想"

### Prompt-as-Code 方法论

将 Prompt 工程从文本编辑升级为软件工程：

```yaml
prompt_as_code:
  architecture:
    - system_prompt/          # 系统提示（版本化）
    - few_shots/              # 示例库（分类管理）
    - context_templates/      # 上下文模板（动态组装）
    - tool_descriptions/      # 工具描述（精确编写）
    - evaluation_criteria/    # 评估标准（量化定义）
    - guardrails/             # 安全边界（强制执行）
    
  version_control:
    tool: "git"
    strategy: "prompt_changelog"
    
  testing:
    tool: "automated_eval"
    strategy: "regression_testing"
```

### 6 组件 Prompt 架构 + 4 层防御策略

```
┌──────────────────────────────────────────────┐
│            6-Component Prompt Architecture    │
│  1. System Instructions                      │
│  2. Context Injection Points                 │
│  3. Tool Definitions                         │
│  4. Output Specifications                    │
│  5. Guardrails                               │
│  6. Evaluation Criteria                      │
├──────────────────────────────────────────────┤
│            4-Layer Defense Strategy           │
│  Layer 1: Input Validation (格式校验)         │
│  Layer 2: Context Window Management          │
│  Layer 3: Output Verification                │
│  Layer 4: Feedback Loop Integration          │
└──────────────────────────────────────────────┘
```

### Karpathy 类比深化

> **Andrej Karpathy**："LLM 是 CPU、上下文窗口是 RAM、你是操作系统。"

这个类比的核心含义：
- **CPU（LLM）**：处理能力有限但可升级（换模型）
- **RAM（上下文窗口）**：容量有限，需要精心管理
- **OS（你/Harness）**：负责调度、内存管理、I/O 操作

---

## 24. Agent Harness 原型实践（2026-04-02 更新）

**来源**：[htek.dev - Agent Harnesses: Why 2026 Isn't About More Agents](https://htek.dev/articles/agent-harnesses-controlling-ai-agents-2026/)

### 核心论点

> 2026 年的真正工程挑战不是构建更多 Agent，而是构建控制它们的基础设施。

### Harness 原型实现

作者实际构建了一个 Harness 原型，包含以下关键功能：

1. **工具拦截**：在 Agent 调用工具前进行拦截和验证
2. **预算控制**：限制 token 使用和 API 调用次数
3. **迭代循环**：支持 Agent 的多轮迭代改进

```yaml
harness_prototype:
  tool_interception:
    enabled: true
    strategy: "whitelist_with_validation"
    
  budget_control:
    max_tokens_per_task: 50000
    max_api_calls: 100
    alert_threshold: 80%
    
  iteration_loop:
    max_iterations: 5
    exit_condition: "quality_score >= 8 or budget_exhausted"
```

### 管理的关键维度

| 维度 | 管理方式 | 目的 |
|------|---------|------|
| **生命周期** | 启动/暂停/停止/重启 | 长时任务管理 |
| **上下文窗口** | 压缩/重置/隔离 | 防止信息过载 |
| **工具访问** | 白名单/拦截/验证 | 安全边界控制 |
| **安全边界** | 沙箱/权限/审计 | 防止越权操作 |

---

## 25. Phil Schmid Context Engineering Part 2：Context Rot 解决方案（2026-04-03 更新）

**来源**：[Phil Schmid - Context Engineering for AI Agents Part 2](https://www.philschmid.de/context-engineering-part-2)

### Context Compaction 可逆化

传统上下文压缩是不可逆的——压缩后信息丢失。Phil Schmid 引入了 Manus 的 Peak Ji 分享的创新实践：

> 压缩后的信息可以通过工具读回文件，实现"可逆压缩"。

```yaml
compaction_reversibility:
  strategy: "compress_but_keep_accessible"
  
  steps:
    - name: "compress"
      action: "生成上下文摘要"
      side_effect: "将原始信息写入文件系统"
      
    - name: "recall"
      action: "需要原始信息时，通过工具读取文件"
      trigger: "agent 需要历史细节"
```

### 子代理上下文隔离

> 避免子代理继承全局 KV-cache，减少无关信息干扰。

```yaml
sub_agent_isolation:
  strategy: "minimal_context_handoff"
  
  principles:
    - "子代理只接收与其任务相关的上下文"
    - "不共享全局完整历史"
    - "任务完成后，子代理的中间状态不回传"
    - "只返回最终结果和关键决策"
```

### 分层路由策略

不同复杂度的任务应路由到不同配置的子代理：

```yaml
routing_strategy:
  simple_tasks:
    model: "fast-model"
    context: "minimal"
    examples: []
    
  complex_tasks:
    model: "reasoning-model"
    context: "rich"
    examples: ["relevant_few_shots"]
    
  research_tasks:
    model: "deep-reasoning"
    context: "full_background"
    tools: ["web_search", "document_reader"]
```

### 实践要点

1. **可逆压缩 > 不可逆压缩**：保留文件层面的完整信息，仅在上下文窗口中保留摘要
2. **隔离是效率的基础**：子代理不需要知道全局上下文
3. **路由即优化**：简单任务用快模型，复杂任务用强模型

---

## 26. Context Engineering 利用率阈值管理（2026-04-03 更新）

**来源**：[State of Context Engineering in 2026 (Medium)](https://medium.com/@kushalbanda/state-of-context-engineering-in-2026-cf92d010eab1)

### 五大核心模式回顾

| 模式 | 核心目标 | 层级 |
|------|---------|------|
| **Progressive Disclosure** | 渐进式加载 | Entry Control |
| **Compression** | 压缩累积历史 | Runtime Management |
| **Routing** | 分层路由到专门子代理 | Runtime Management |
| **Evolved Retrieval** | 按需外部知识获取 | Knowledge Layer |
| **Tool Management** | 控制能力表面 | Entry Control |

### 关键阈值：60% 利用率规则

> **上下文利用率超过 60% 时**，应按以下优先级处理：

```
1. 摘要历史 → 释放对话空间
2. 过滤检索 → 精简 RAG 结果
3. 动态路由工具 → 隐藏不活跃工具
4. 压缩步骤结果 → 清理工具返回值
```

### 生产环境分层叠加策略

```
┌─────────────────────────────────────────────┐
│          Progressive Disclosure              │
│          + Tool Management                   │
│    → 控制进入上下文窗口的内容                  │
├─────────────────────────────────────────────┤
│          Routing + Compression               │
│    → 管理执行期间的内容                        │
├─────────────────────────────────────────────┤
│          Retrieval                           │
│    → 按需补充外部知识                          │
└─────────────────────────────────────────────┘
```

### 实践要点

1. **60% 是警戒线**：不要等到 80%+ 才开始处理，那时已经太晚
2. **优先级明确**：先压缩历史，再精简工具，最后才考虑隔离
3. **分层叠加**：不是选一个，而是按需组合

---

## 27. AI 编码的真正瓶颈是上下文，而非模型能力（2026-04-04 更新）

**来源**：[The New Stack - Context is AI Coding's Real Bottleneck in 2026](https://thenewstack.io/context-is-ai-codings-real-bottleneck-in-2026/)

### 核心论点

> AI 编码的真正瓶颈不是模型能力，而是**上下文**——工程师脑中掌握的知识与 AI 能理解的信息之间的鸿沟。

### 认知模式转变

阅读 AI 生成代码需要**不同于阅读人类代码的认知工作**：

| 阅读人类代码 | 阅读 AI 生成代码 |
|-------------|-----------------|
| 跟随同事的推理过程 | 从输出**逆向工程意图** |
| 代码即思维映射 | 代码是黑盒输出 |
| 理解上下文自然连贯 | 需要主动填补上下文鸿沟 |

### 上下文鸿沟的系统性挑战

> 让 AI 工具获取正确上下文需要**系统化的刻意努力**，多数团队尚未建立方法论。

**关键障碍**：
1. **隐性知识外化**：工程师脑中的设计决策、约束条件、历史背景很难显式传达给 Agent
2. **动态上下文维护**：项目上下文持续变化，静态文档很快过时
3. **跨团队知识同步**：不同团队成员对项目的理解存在差异

### 成功团队的工作节奏

```
┌─────────────────────────────────────────────┐
│              人类                           │
│  ┌──────────────┐  ┌──────────────────┐    │
│  │  判断性工作   │  │  创造性工作       │    │
│  │  架构决策     │  │  新功能设计       │    │
│  │  安全审查     │  │  用户体验优化     │    │
│  └──────────────┘  └──────────────────┘    │
├─────────────────────────────────────────────┤
│              AI Agent                       │
│  ┌──────────────┐  ┌──────────────────┐    │
│  │  重复性任务   │  │  模式化实现       │    │
│  │  代码生成     │  │  测试编写         │    │
│  │  Bug 修复     │  │  文档更新         │    │
│  └──────────────┘  └──────────────────┘    │
└─────────────────────────────────────────────┘
```

### 实践要点

1. **投资上下文基础设施**：与其追求更强的模型，不如建立更好的上下文传递机制
2. **建立逆向工程思维**：团队需要培养从 AI 输出推导意图的能力
3. **方法论优先于工具**：工具只是载体，系统化的上下文管理方法论才是核心
4. **人机协作节奏**：明确划分判断/创造性工作（人类）和重复/模式化工作（AI）

---

*更新时间：2026-04-04*

---

## 44. Sourcegraph：Context Engineering 实战指南（2026-08-07 更新）

**来源**：[Sourcegraph - Context Engineering: A Practical Guide for AI Agents](https://sourcegraph.com/blog/context-engineering)

### 核心命题

> Context Engineering 的本质是「**如何在有限 token 预算和注意力预算下为 Agent 提供正确的上下文**」。

### 关键实践洞察

#### 第 47 步决策残留问题

> Agent 在第 47 步决策时，前 1-46 步的残留信息仍在上下文窗口中——必须主动管理。

这量化说明：上下文不是「过去就过去了」，而是**累积污染**。每一步操作都会在后续决策中产生噪音。

#### Sourcegraph 7.0 = 开发者和 Agent 的共享智能层

Sourcegraph 7.0 将代码智能平台重新定位为「开发者和 AI Agent 的共享智能层」——
解决 Agent 和人类共同面临的挑战，即：
- 跨仓库依赖理解
- 历史决策的上下文追溯
- 未记录的架构模式发现

> **关键认知**：企业代码库中 Agent 和人类面临完全相同的上下文挑战——解决一端即解决两端。

### 实践要点

1. **上下文是累积的**：不是每步独立，而是历史累积影响当前决策
2. **共享智能层思维**：为 Agent 构建的上下文基础设施同时惠及人类工程师
3. **跨仓库上下文是核心挑战**：单体仓库内的搜索不够，需要跨仓库依赖和历史决策追踪

---

## 45. Packmind/Stanford ACE：上下文增量更新实证（2026-08-07 更新）

**来源**：[Packmind - Context Engineering Best Practices for AI-Powered Dev Teams](https://packmind.com/context-engineering-ai-coding/context-engineering-best-practices)（基于 Stanford/SambaNova ACE 论文，2025 年 10 月）

### 实证研究核心发现

基于 Stanford/SambaNova ACE 论文的量化数据，为 Context Engineering 实践提供了实证支撑：

#### 1. 上下文是可编程、可治理的智能层

> 上下文文件可以版本化、审计、协作演进——应视为生产代码来管理。

这意味着 Context Engineering 不再是「写得好不好」的艺术问题，而是**可工程化、可治理的系统问题**。

#### 2. 增量更新 >> 全量重写

| 指标 | 增量更新 | 全量重写 |
|------|---------|--------|
| 漂移率 | ↓ 86% | 基线 |
| 延迟 | ↓ 86% | 基线 |

> **关键发现**：增量更新上下文文件比全量重写减少 **86%** 的漂移和延迟。

#### 3. 结构化上下文推升开源模型表现

> 结构化维护的上下文在准确率上显著优于静态提示，可将**开源模型推至接近前沿模型**的表现。

这与 Faros.ai 的 211 任务评测结论一致：优化的 Harness 可以让开源模型达到甚至超过昂贵的前沿模型。

### 实践建议

```yaml
context_management:
  principle: "treat_as_production_code"
  
  version_control:
    - "上下文文件纳入 Git 版本控制"
    - "变更需要 Code Review"
    - "维护 CHANGELOG"
    
  update_strategy:
    - "增量更新优先，避免全量重写"
    - "定期审计上下文新鲜度"
    - "CI 集成验证一致性"
```

### 实践要点

1. **上下文文件 = 生产代码**：需要版本控制、Code Review、CI 验证
2. **增量更新减少 86% 漂移**：不要每次全量重写上下文，而是做精确的增量修改
3. **开源模型 + 好上下文 ≈ 前沿模型**：投资上下文质量的 ROI 可能高于购买更贵的模型
4. **可审计性**：上下文的每次变更都应可追溯、可回滚

---

*更新时间：2026-08-07*

---

## 28. Mem0 Context Engineering 完整指南：记忆系统的系统化方法（2026-04-05 更新）

**来源**：[Mem0 - Context Engineering for AI Agents: Complete Guide](https://mem0.ai/blog/context-engineering-ai-agents-guide)

### 核心定义

> Context Engineering 定义为"**结构化上下文和记忆使 AI 系统随时间智能行为的系统方法**"。

Mem0 从记忆系统角度重新审视 Context Engineering，将其核心支柱拆解为：

| 支柱 | 描述 | 关键技术 |
|------|------|---------|
| **记忆管理** | 跨交互持久化信息 + 语义检索 | 向量存储、时序索引 |
| **RAG 增强** | 检索与压缩结合 | 混合检索、上下文窗口优化 |
| **智能格式化** | 精确时机传递精确信息 | 动态模板、条件注入 |

### Mem0 记忆压缩引擎架构

```
用户输入 → 写入持久存储
                ↓
         语义搜索选择相关记忆
                ↓
         智能压缩（去重、合并、优先级排序）
                ↓
         注入 LLM 上下文
                ↓
         不同记忆类型隔离（用户/会话/Agent 级别）
```

### 实践要点

1. **记忆不是缓存**：记忆需要语义检索能力，而非简单的 key-value 存储
2. **隔离不同记忆类型**：用户偏好、会话状态、Agent 知识应分别管理
3. **压缩而非截断**：智能压缩（去重+合并+优先级）优于暴力截断
4. **RAG + Memory 融合**：检索增强生成与持久记忆结合，形成完整的上下文策略

### 与其他 Context Engineering 方法的对比

| 方法 | 来源 | 核心关注点 |
|------|------|-----------|
| **六层上下文模型** | Phil Schmid | 系统指令→长期记忆→检索文档→工具定义→对话历史→当前任务 |
| **Context Rot 解决方案** | Phil Schmid Part 2 | 可逆压缩、子 Agent 隔离、128K 阈值触发摘要 |
| **记忆压缩引擎** | Mem0 | 持久化 + 语义检索 + 智能压缩 + 记忆类型隔离 |

### 关键洞察

> Mem0 的方法与 Phil Schmid 的 Context Engineering 系列形成互补：Phil 侧重信息流的架构设计，Mem0 侧重记忆的持久化和检索策略。二者结合构成完整的 Context Engineering 实践框架。

---

*更新时间：2026-04-05*

---

## 29. HumanLayer：Harness Engineering 是 Context Engineering 的子集（2026-04-08 更新）

**来源**：[HumanLayer - Skill Issue: Harness Engineering for Coding Agents](https://www.humanlayer.dev/blog/skill-issue-harness-engineering-for-coding-agents)

### 核心论点

HumanLayer CTO 撰文将 Harness Engineering 定义为 **Context Engineering 的子集**——主要通过四个杠杆来管理编码 Agent 的上下文窗口：

| 杠杆 | 描述 | 示例 |
|------|------|------|
| **系统提示** | 控制 Agent 的行为边界和决策策略 | AGENTS.md、CLAUDE.md |
| **工具/MCP 选择** | 限制 Agent 可调用的工具集 | 只开放文件系统工具 |
| **子 Agent** | 上下文隔离和封装 | Explore 子 Agent、Bash 子 Agent |
| **钩子** | 生命周期事件拦截 | pre-tool-call、post-response |

### Claude Code 上下文封装模式

文章详细分析了 Claude Code 的 Explore/Bash 子 Agent 如何实现**上下文封装**：

```
主 Agent 上下文窗口
├─ 系统提示（行为规则）
├─ 工具定义（能力边界）
├─ 子 Agent 结果（压缩后的外部信息）
└─ 对话历史（当前任务进展）

子 Agent 上下文窗口（隔离的）
├─ 子任务指令
├─ 子任务专用工具
└─ 子任务执行结果
    ↓
结果摘要返回主 Agent
```

### 自定义 MCP Server 模式

文章推荐通过自定义 MCP Server 实现类似 Claude Code 的 Harness 模式：

```yaml
# 自定义 Harness MCP Server 示例
mcp_servers:
  - name: codebase-context
    tools:
      - search_codebase  # 封装代码搜索
      - read_file_summary # 压缩文件内容
      - list_dependencies # 精准依赖图
    hooks:
      - pre_call: validate_scope  # 限制访问范围
      - post_call: summarize_result  # 压缩返回结果
```

### 关键洞察

> Harness Engineering 的核心在于**精确控制进入上下文窗口的信息**。通过系统提示设定边界、工具选择限制能力、子 Agent 隔离执行、钩子拦截生命周期——四个杠杆协同工作，将庞大的工程环境压缩为 Agent 可有效处理的上下文。

### 实践要点

1. **上下文窗口是最稀缺资源**：每个 token 都要精打细算
2. **子 Agent = 上下文防火墙**：将大任务分解为上下文可控的子任务
3. **MCP Server = 自定义 Harness**：通过工具和钩子构建精准的上下文边界
4. **系统提示是契约**：明确 Agent 能做什么、不能做什么、应该怎么做

---

## 30. Sud Shekhar：从 Prompts 到 Context Engineering 自主 Agent（2026-04-08 更新）

**来源**：[Sud Shekhar - From Prompts to Context: Mastering Context Engineering for Autonomous AI Agents](https://www.sudshekhar.com/blog/from-prompts-to-context-mastering-context-engineering-for-autonomous-ai-agents)

### 核心论点

> 2026 年可靠 AI 的秘密不仅是更好的提示词，而是 **Context Engineering**——为自主 Agent 架构动态信息环境。

### 从静态提示到动态上下文的演进

```
2024: Prompt Engineering
  └─ 静态文本 → 固定输出

2025: Context Engineering
  └─ 动态环境 → 上下文感知输出

2026: Harness Engineering
  └─ 完整基础设施 → 可靠自主运行
```

### 自主 Agent 的 Context Engineering 关键要素

| 要素 | 描述 | 重要性 |
|------|------|--------|
| **结构化上下文注入** | 不是随意塞入信息，而是按任务阶段精准注入 | 🔴 高 |
| **记忆管理** | 短期（会话）+ 长期（跨会话）记忆的分层管理 | 🔴 高 |
| **状态追踪** | Agent 需要始终知道自己处于任务的哪个阶段 | 🟡 中 |
| **错误恢复** | 上下文丢失后的恢复机制 | 🔴 高 |

### 生产级 Context Engineering 框架

```yaml
context_engineering:
  entry_control:
    - task_decomposition: 将大任务分解为上下文可控的子任务
    - relevance_filtering: 只注入与当前子任务相关的上下文
    
  runtime_management:
    - progress_tracking: 通过文件系统维护进度状态
    - context_window_monitoring: 实时监控上下文利用率
    - automatic_compression: 超过阈值时自动压缩历史
    
  knowledge_layer:
    - long_term_memory: 持久化存储重要决策和知识
    - retrieval_augmentation: 语义检索补充实时上下文
    - self_improvement: 从历史任务中学习和优化
```

### 关键洞察

> 自主 Agent 需要**结构化的上下文注入、记忆管理和状态追踪**才能在生产环境中可靠运行。这不仅是技术问题，更是系统工程问题——需要像设计操作系统一样设计 Agent 的信息环境。

### 实践要点

1. **上下文注入要精准**：按任务阶段注入，不是一次性全部加载
2. **记忆分层管理**：短期记忆解决即时问题，长期记忆积累领域知识
3. **状态追踪是安全网**：Agent 必须能从任何中断点恢复
4. **渐进式构建**：从简单上下文管理开始，逐步增加复杂性

---

*更新时间：2026-04-08*

---

## 46. Agent Harness 安全新前沿：运行时层成为攻击面（2026-08-25 更新）

**来源**：
- [Forkast - AWS AgentCore Harness Bypass](https://forkast.news/aws-agentcore-harness-bypass-exposed-a-cross-platform-vulnerability-class-in-agent-runtimes/)（2026-08-22）
- [Forkast - The Agent Harness Emerges as the New Security Frontier](https://forkast.news/the-agent-harness-emerges-as-the-new-security-frontier/)（2026-08-18）
- 关联：[CoreBreak 绕过 agent guardrail（管道层攻击）](https://forkast.news/corebreak-bypasses-ai-agent-guardrails-at-the-plumbing-layer-and-model-level-defenses-cannot-help/)

### 核心发现

> 针对 AWS AgentCore 的 harness bypass 研究揭示了一类存在于 agent 运行时层的**跨平台漏洞类**——harness 本身成为攻击面，**模型级防御无法兜底**。

- 攻击面从模型层转移到 harness/运行时层
- 与同期 CoreBreak（在"管道层"绕过 agent guardrail）共同指向：harness/运行时安全正在成为独立攻防领域

### Harness 安全治理三支柱

1. **权限**：harness 持有的工具权限需最小化，变更需审计
2. **隔离**：沙箱与运行时边界需独立验证，不能依赖模型自我约束
3. **可观测性**：harness 层操作需完整日志与可追溯，异常行为可回溯定位

### 实践要点

1. **把 harness 当作攻击面做威胁建模**：评估 agent 方案时将 runtime 安全列为独立维度
2. **guardrail 部署在管道层而非仅模型层**：CoreBreak 证明仅靠模型级防御无法拦截管道层攻击
3. **权限-隔离-可观测三支柱检查清单**：任何 harness 选型/自建方案都应逐项过检

---

## 40. Vercel「少即是多」——精心策划的工具集胜过丰富工具集（2026-07-24 更新）

**来源**：[Phil Schmid - Agent Harness 2026](https://www.philschmid.de/agent-harness-2026)（引用 Vercel 实践）

### 核心发现

> Vercel 在构建 v0 编码代理时发现，**删除 80% 的工具反而获得更好的结果**——更多工具意味着模型更多困惑。

这是 Harness Engineering 的核心教训之一：**精心策划的工具集比丰富的工具集更有效**。

### 理论解释

与 Rich Sutton 的「The Bitter Lesson」呼应——通用方法胜过手工编码的知识，过度工程化的约束反而降低性能。

### 量化洞察：管道乘法效应

> 一个 20 步管道即使每步 95% 成功率，端到端完成率仅 **36%**（0.95²⁰ ≈ 0.36）。

这意味着：
- 每增加一个步骤，整体可靠性都在下降
- 工具数量越多，模型选择错误的概率越大
- 精简工具集直接提升每步的成功率

### 实践建议

```yaml
tool_management:
  principle: "less_is_more"
  
  strategy:
    - 定期审查工具使用率，移除使用频率低于 5% 的工具
    - 按任务阶段动态暴露工具子集（而非全量加载）
    - 合并功能重叠的工具
    - 工具描述力求精确而非详尽
    - 用评估驱动工具增减决策（而非直觉）
```

### 实践要点

1. **工具数量不是能力标志**：80% 的工具可能只增加噪音
2. **动态暴露 > 静态全量**：根据 Agent 当前阶段切换工具集
3. **评估驱动增减**：用 A/B 测试验证工具增减的效果
4. **与 Bitter Lesson 一致**：通用方法 + 更少硬编码 > 专用工具堆砌

---

## 41. 「模型是商品，Harness 是护城河」（2026-07-24 更新）

**来源**：[Harness Engineering Blog - Agent Harness Complete Guide](https://harness-engineering.ai/blog/agent-harness-complete-guide/)

### 核心论点

> 随着模型能力趋同和开源模型的普及，**模型本身不再是竞争优势，Harness 才是**。

### 六大核心组件定义

该文章定义了 Harness 的六大核心组件：

| 组件 | 职责 | 护城河价值 |
|------|------|------------|
| **上下文管理** | 管理信息流入和流出 Agent | 直接决定 Agent 决策质量 |
| **工具编排** | 工具选择、调用和结果处理 | 积累领域特定工具链 |
| **生命周期管理** | Agent 启动、暂停、恢复、终止 | 长时任务可靠性 |
| **子代理调度** | 任务分解和并行执行 | 复杂任务编排能力 |
| **验证循环** | 输出质量检查和纠正 | 防止错误累积 |
| **可观测性** | 监控、日志和追踪 | 持续优化基础 |

### Manus 的经验佐证

- Manus 花了 **六个月和五次完整重写** 才达到生产就绪
- LangChain 的 Open Deep Research 一年内重写了三次架构
- 这些案例证明：构建可靠 Harness 的时间远超选择模型的努力

### 实践要点

1. **竞争优势在于积累**：Harness 的护城河来自持续优化的领域知识、工具链和验证循环
2. **模型可替换，Harness 不可替换**：切换模型只需改配置，重建 Harness 需要数月
3. **投资回报率**：Harness 优化的 ROI 随时间增长，模型选择的 ROI 随新模型发布递减

---

## 23. Anthropic：有效上下文工程补充实践（2026-06-13 更新）

**来源**：[Anthropic - Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

Anthropic 进一步强调上下文工程是系统性管理整个上下文状态的策略，补充了以下关键实践：

### 工具结果清除是最轻量的上下文压缩

- Agent 完成工具调用后，大体积返回值应及时从上下文中清除
- 这比复杂的摘要压缩更简单、更可控
- 防止历史消息无限膨胀挤占系统指令和工具定义空间

### 基于文件的内存系统

- 利用文件系统在上下文窗口之外持久化存储信息
- Agent 可按需查询，避免一次性加载全部历史
- Claude Code 使用这种方式对大型数据库执行复杂数据分析

### 上下文感知数据分析

- Claude Code 采用上下文感知方法处理大型数据集
- 不是将整个数据库塞入上下文，而是按需查询和渐进分析
- 核心思路：**让 Agent 控制信息流入，而非被动接收**

---

## 24. Sourcegraph：Context Engineering 四大支柱框架（2026-06-13 更新）

**来源**：[Sourcegraph - Context Engineering](https://sourcegraph.com/blog/context-engineering)

### Prompt Engineering vs Context Engineering

> **关键区分**：Prompt Engineering 关注的是**一个句子**怎么写；Context Engineering 关注的是**产生该句子及其周围一切的整个流水线**。

### 四大支柱

| 支柱 | 核心职责 | 实践要点 |
|------|---------|---------|
| **上下文收集** | 获取相关信息 | 代码搜索、文档检索、依赖分析 |
| **上下文过滤** | 去噪提纯 | 相关性评分、优先级排序、冗余剔除 |
| **上下文组装** | 结构化组织 | 按任务阶段排列、控制信息密度 |
| **上下文维护** | 动态更新 | 状态追踪、过期清理、增量刷新 |

### 对 Coding Agent 的实践指导

1. **构建可靠的上下文管线**：从代码仓库 → 相关文件 → 精确符号的流水线
2. **生产环境要求**：上下文质量直接决定代码生成质量，需要严格的质量控制
3. **可观测性**：监控每次 Agent 调用的上下文构成，定位信息瓶颈

---

## 25. Manus 团队：构建 AI Agent 的上下文工程经验教训（2026-06-13 更新）

**来源**：[Manus - Context Engineering for AI Agents: Lessons from Building Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus)

Manus 团队从生产级 AI Agent 开发中总结的实战经验：

### 1. KV-cache 优化

- 相同前缀的上下文可以大幅降低 TTFT（首 Token 延迟）和推理成本
- **实践**：将系统指令、工具定义等稳定部分放在上下文前部，动态内容放在后部
- 这样即使对话历史变化，前缀缓存仍然有效

### 2. 上下文感知状态机

- 使用状态机管理每个阶段可用工具的子集
- **关键问题**：上下文中残留已失效的工具描述会让模型混淆
- **解决方案**：根据 Agent 当前状态动态调整工具列表，而非始终暴露全部工具

### 3. 节奏陷阱（Cadence Trap）

> Agent 倾向于重复相似动作，仅仅因为上下文中看到的就是这些——这叫"节奏陷阱"。

- **症状**：Agent 在某个模式上循环，不主动探索新方向
- **原因**：上下文惯性——模型倾向于延续已有模式
- **解决方案**：在 harness 中注入多样性信号，如随机采样策略、显式的"打破循环"指令

### 生产环境建议

```yaml
harness_anti_patterns:
  kv_cache_optimization:
    - stable_prefix: "系统指令 + 工具定义放在前部"
    - dynamic_suffix: "对话历史和中间结果放在后部"
    
  tool_state_machine:
    - per_stage_tools: "按阶段暴露工具子集"
    - cleanup: "移除已失效工具描述"
    
  cadence_breaker:
    - diversity_signal: "注入随机探索指令"
    - loop_detection: "检测重复模式并主动干预"
    - explicit_reset: "定期清理上下文重启节奏"
```

---

## 26. Packmind：Context Engineering 完整实践指南（2026-06-14 更新）

**来源**：[Packmind - Context Engineering Best Practices](https://packmind.com/context-engineering-ai-coding/context-engineering-best-practices)

Packmind 编译了 30+ 可操作的上下文工程实践，覆盖从编写有效的上下文文件到构建大规模 ContextOps 基础设施的完整链路。

### 行业现状数据

- **91%** 的工程组织已采用至少一种 AI 编码工具
- **84%** 开发者使用 AI 工具
- **41%** 代码由 AI 生成或辅助

### 核心问题定位

> **问题不是模型能力不足**——工具很快，但输出不遵循团队的约定、架构决策和构建方式。

上下文工程的目标是让 AI 编码工具产出符合团队标准的代码，而非各自为战的 "AI slop"。

### 实践框架

#### 第一层：上下文文件（Context Files）

- **AGENTS.md 规范**：作为 Agent 的入口文档，描述项目结构、约定和关键约束
- **分层上下文注入**：根目录 → 模块目录 → 组件目录，逐级细化上下文
- **约定文件**：编码规范、架构决策记录（ADR）、API 设计指南

#### 第二层：上下文窗口管理（Context Window Management）

- **Token 预算分配**：系统指令 ~20%、工具定义 ~30%、任务上下文 ~40%、生成空间 ~10%
- **渐进式加载**：先加载摘要，按需展开详情
- **上下文压缩**：对长对话历史进行结构化摘要

#### 第三层：团队级 ContextOps

- **约定即代码**：将团队编码标准转化为 Agent 可读的规则文件
- **上下文版本管理**：随代码库演进的上下文文件版本控制
- **CI 集成验证**：在 CI 中验证上下文文件的新鲜度和一致性

### 与其他最佳实践的关系

本指南与 Anthropic 上下文工程指南（§23）和 Sourcegraph 四大支柱框架（§24）形成互补：
- Anthropic 侧重**策略和工具**（Memory Tool、文件系统）
- Sourcegraph 侧重**流程和架构**（收集-过滤-组装-维护）
- Packmind 侧重**团队落地和规模化**（AGENTS.md、ContextOps、CI 集成）

## 27. Context Engineering 状态：2026 年五大核心模式

**来源**：[Towards AI - State of Context Engineering in 2026](https://pub.towardsai.net/state-of-context-engineering-in-2026-cf92d010eab1)

### 五大模式

1. **渐进式披露（Progressive Disclosure）**：按需展示信息，避免一次性淹没上下文窗口
2. **压缩（Compression）**：对长对话历史和工具输出进行结构化摘要
3. **路由（Routing）**：根据任务类型将请求分发到不同的上下文配置
4. **演进检索（Evolved Retrieval）**：从简单向量搜索演进到混合检索、重排序等高级策略
5. **工具管理（Tool Management）**：动态控制工具定义的加载，减少 token 占用

### 技能提取（Skill Extraction）

当 Agent 反复处理同类任务时，可将处理模式提取为新的 skill 文件，动态切换 Agent 身份。这是 Harness Engineering 中「知识沉淀」的关键机制。

### 竞争注意力问题

> 上下文窗口中的每个 token 都在竞争模型注意力——系统指令、工具定义、MCP 资源、检索文档、对话历史和累积操作历史都需纳入管理。

---

## 28. Context Engineering 四大支柱（Sourcegraph）

**来源**：[Sourcegraph - Context Engineering](https://sourcegraph.com/blog/context-engineering)

### 定义对比

- **Prompt Engineering** 关注单句——如何措辞一个提示
- **Context Engineering** 关注产出该句的整个管线——如何为 Agent 组装正确的信息集

### 四大支柱

| 支柱 | 说明 |
|------|------|
| **上下文组装（Assembly）** | 收集正确的文件、工具定义、对话历史切片和检索事实 |
| **上下文压缩（Compaction）** | 防止上下文窗口在自身重量下崩溃，结构化摘要 |
| **上下文路由（Routing）** | 根据任务类型选择不同的上下文配置策略 |
| **上下文验证（Verification）** | 验证上下文文件的新鲜度、一致性和正确性 |

### 实践要点

需要为 Agent 提供：正确的文件、工具定义、对话历史切片和检索事实，同时防止上下文窗口在自身重量下崩溃。

---

## 29. Agent 可靠性手册：Context Engineering 工程杠杆

**来源**：[Digital Applied - Context Engineering Agent Reliability Playbook 2026](https://www.digitalapplied.com/blog/context-engineering-agent-reliability-playbook-2026)

### 四种 Agent 特有失败模式

1. **上下文退化（Context Degradation）**：随对话增长，早期关键信息被「遗忘」
2. **工具结果污染（Tool Result Pollution）**：大量工具输出淹没关键信号
3. **历史膨胀（History Bloat）**：累积的操作历史占用过多 token 预算
4. **记忆冲突（Memory Conflicts）**：不同轮次存储的信息相互矛盾

### 四个工程杠杆

1. **Token 预算分配**：为不同类型上下文设定明确的 token 配额
2. **压缩决策规则**：基于触发条件自动执行上下文压缩
3. **多 Agent 隔离模式**：用子 Agent 隔离上下文，防止交叉污染
4. **渐进式上下文加载**：先加载摘要，按需展开详情

### Anthropic 定义引用

> "Context Engineering 是在 LLM 推理过程中策划和维护最佳 token 集合的策略集合。"

有效 Agent 开发需要在*上下文中思考*，而非仅关注系统提示词写得好不好。

---

## 31. Anthropic 与 OpenAI Agent 架构趋同分析（2026-07-05 更新）

**来源**：[Medium - Anthropic and OpenAI Just Shipped the Same Answer to AI Agents Seven Days Apart](https://medium.com/@rajasekar-venkatesan/anthropic-and-openai-just-shipped-the-same-answer-to-ai-agents-seven-days-apart-c19f2dc03244)

### 行业共识里程碑

2026 年 4 月，Anthropic 和 OpenAI 在七天内独立发布了几乎相同的 Agent 架构方案——这是 Agent 工程领域的重要趋同信号。

### 共同架构要素

| 要素 | Anthropic Managed Agents | OpenAI Agents SDK |
|------|------------------------|-------------------|
| **沙箱执行** | ✅ 安全隔离执行环境 | ✅ Sandbox 模块 |
| **检查点** | ✅ 状态持久化 | ✅ Session 管理 |
| **凭据隔离** | ✅ 安全凭据管理 | ✅ 凭据作用域控制 |
| **端到端追踪** | ✅ 全链路可观测 | ✅ Tracing 集成 |
| **控制/计算平面分离** | ✅ 编排与执行解耦 | ✅ Handoff 机制 |

### 关键洞察

> 两家顶级 AI 公司在七天内独立发布几乎相同的架构，说明生产级 Agent 需求已经形成了行业共识——这不是偶然巧合，而是问题空间的客观约束决定了架构选择。

### 实践要点

1. **沙箱不再是可选项**：生产级 Agent 必须有安全隔离的执行环境
2. **检查点驱动可靠性**：通过状态持久化实现故障恢复和长时任务管理
3. **凭据隔离是安全底线**：Agent 不应直接接触用户凭据
4. **全链路追踪是调试基础**：没有可观测性就没有生产可靠性

---

## 32. 从 Prompt 到 Context 到 Harness：四年 AI Agent 模式演进（2026-07-05 更新）

**来源**：[Bits-Bytes-NN - Evolution of AI Agentic Patterns](https://bits-bytes-nn.github.io/insights/agentic-ai/2026/04/05/evolution-of-ai-agentic-patterns-en.html)

### 三次范式迁移（2022-2026）

```
2022-2023: Prompt Engineering
  └─ 核心技能：写好提示词
  └─ 关键产物：System Prompt、Few-shot Examples
  └─ 评价指标：Prompt 质量

2024-2025: Context Engineering
  └─ 核心技能：管理上下文状态
  └─ 关键产物：RAG、记忆系统、工具定义
  └─ 评价指标：检索精度、上下文利用率

2026: Harness Engineering
  └─ 核心技能：构建完整运行时基础设施
  └─ 关键产物：沙箱、子 Agent 编排、生命周期管理、评估闭环
  └─ 评价指标：KV-cache 命中率、harness 复杂度、任务成功率
```

### 核心发现

> 工程严谨性没有消失，只是转移了位置——2026 年的关键指标不是 prompt 质量，而是 KV-cache 命中率和 harness 复杂度。

### 实践要点

1. **Prompt 质量不再是关键瓶颈**：在推理模型时代，prompt 的边际价值在下降
2. **上下文管理成为核心能力**：如何为 Agent 提供正确的信息集比如何写提示更重要
3. **Harness 是新的竞争壁垒**：完整的运行时基础设施决定了 Agent 的生产可靠性
4. **能力栈在升高**：每一层范式都没有消失，而是被上层封装和自动化

---

## 33. ClickHouse：12 框架 MCP 集成对比（2026-07-05 更新）

**来源**：[ClickHouse - How to Build AI Agents with MCP: 12 Frameworks Compared](https://clickhouse.com/blog/how-to-build-ai-agents-mcp-12-frameworks)

### MCP 已成为 Agent 集成的事实标准

ClickHouse 对 12 个主流框架的 MCP 集成方式进行了系统对比，覆盖 OpenAI、Gemini、Vertex AI 等平台。

### 关键框架对比

| 框架 | MCP 集成特点 | 最佳场景 |
|------|-------------|--------|
| **Claude Agent SDK** | 安全优先，生产环境导向 | Anthropic 生态、安全敏感场景 |
| **OpenAI Agents SDK** | 委托模式（Delegation Pattern） | OpenAI 生态、快速原型 |
| **CrewAI** | 多 Agent 工作流深度集成 | 角色协作、多 Agent 编排 |
| **LangChain** | 生态广度，最多连接器 | 异构系统集成 |

### 关键发现

1. **MCP 标准已确立**：GitHub、AWS、ClickHouse 等大型平台均构建了各自的 MCP Server
2. **集成深度差异显著**：从简单的工具调用到完整的工作流集成，各框架差异很大
3. **安全模型不统一**：不同框架对认证、授权和沙箱的处理方式差异大
4. **生态碎片化风险**：虽然 MCP 统一了协议，但实现细节碎片化

### 实践要点

1. **选择框架时评估 MCP 集成深度**：不仅看是否支持，还要看集成方式
2. **安全优先**：生产环境优先选择 Claude Agent SDK 等安全导向框架
3. **生态匹配**：选择与现有技术栈匹配的框架降低集成成本
4. **关注 MCP Server 质量**：MCP Server 的实现质量直接影响 Agent 效果

---

## 42. Phil Schmid：编写 Agent Skills 的 8 条建议（2026-07-29 更新）

**来源**：[Phil Schmid - 8 Tips for Writing Agent Skills](https://www.philschmid.de/agent-skills-tips)（2026-04-13）

Google DeepMind 工程师 Phil Schmid 基于实践经验总结了编写 SKILL.md 文件的 8 条核心建议。

### 核心原则

#### 1. Description 是触发器，不是描述

> SKILL.md 的 frontmatter description 决定何时激活技能。仅优化 description 就能看到 **50% 性能提升**。

```yaml
# ❌ 太模糊
description: "Helps with files"

# ❌ 太宽泛
description: "Does everything you need"

# ✅ 精确触发
description: "Read and write Feishu/Lark documents via API. Activate when user mentions Feishu docs, cloud docs, or docx links."
```

#### 2. 写指令，不写散文

- 用祈使句（"Always use X()"），而非描述性语言
- 5 行代码示例胜过 5 段解释
- Agent 遵循指令比理解概念更可靠

#### 3. 保持精简：500 行上限

> SKILL.md body 控制在 **500 行以内**；超出部分拆分到 `references/` 供按需加载，节省 context。

```markdown
my-skill/
├── SKILL.md          # ≤500 行，核心指令
├── references/       # 按需加载的详细文档
│   ├── api-spec.md
│   └── examples.md
└── scripts/         # 可执行脚本
```

#### 4. 给自由，不给步骤

> 描述**目标**而非**步骤**，让 Agent 自适应错误和发现更好方案。

```markdown
# ❌ 过度规定步骤
1. First call API X
2. Then parse result
3. Then write to file Y

# ✅ 描述目标
Create a document in the specified folder. Handle errors gracefully and retry if needed.
```

### 实践要点

1. **description 是最重要的字段**：50% 性能提升仅来自优化 description
2. **精简是纪律**：500 行硬上限，超出即拆分
3. **指令式 > 描述式**：Agent 更擅长遵循命令而非理解散文
4. **目标导向 > 步骤导向**：给 Agent 自由度反而效果更好

---

## 43. Anthropic：长时运行 Harness 最佳实践精要（2026-07-29 更新）

**来源**：[Anthropic - Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)（2025-11-26）

Anthropic 在长时运行 Agent Harness 设计方面的核心实践总结，与之前已有的 §10 互补，此处聚焦四个关键设计决策。

### 两段式 Harness 设计

> 初始化 Agent 负责首次环境搭建，Coding Agent 负责后续增量开发——不同模式、不同提示词。

```yaml
harness:
  initializer_agent:
    target: "首次运行"
    outputs:
      - init.sh              # 环境初始化脚本
      - claude-progress.txt  # 进度文件
      - initial_git_commit   # 基线代码
    prompt_focus: "项目设置和结构"
    
  coding_agent:
    target: "后续运行"
    prompt_focus: "增量开发和功能实现"
    behavior: "读取进度文件 → 做一个增量改进 → 更新进度文件 → git commit"
```

### 结构化进度文件作为 Agent 间信息传递机制

灵感来自优秀工程师的日常实践——每日进度记录和 git history。

```markdown
# claude-progress.txt

## Session 1 (2026-07-29)
- Set up project structure with Vite + React
- Created base components (Layout, Header, Footer)
- Configured ESLint + Prettier
- Next: Implement authentication module

## Session 2 (2026-07-29)
- Implemented JWT-based login
- Added protected route middleware
- Next: Add role-based permission system
```

**关键**：新 session 通过阅读进度文件 + git log 快速理解工作状态，无需完整对话历史。

### 增量优先原则

> 每个 session 只做增量进步，结束后保持「可合并到 main 分支」的干净状态。

- 不追求单个 session 完成大功能
- 每个 session 结束时代码必须可运行
- git commit 作为天然的检查点

### Context Reset > Compaction

对于表现出强烈 context anxiety 的模型（如 Sonnet 4.5），**完全清除 context 并通过 artifact 交接比压缩总结更有效**。

| 方案 | 适用场景 | 效果 |
|------|---------|------|
| **Compaction** | 轻度 context anxiety | 保持连续性，但可能残留噪音 |
| **Context Reset** | 强 context anxiety | 干净开始，通过 artifact 完整交接 |

**推荐**：当模型出现以下症状时切换到 Context Reset：
- 过早结束任务
- 重复已完成的工作
- 丢失关键约束信息

---

*更新时间：2026-07-29*

---

## 34. Addy Osmani：Agent Harness Engineering 深度拆解（2026-07-06 更新）

**来源**：[Addy Osmani - Agent Harness Engineering](https://addyosmani.com/blog/agent-harness-engineering/)

Google 工程师 Addy Osmani 对 Harness Engineering 做了系统性拆解，提供了多个关键量化洞察。

### 核心发现：「98% 是 Harness，而非模型」

> 一个 Claude Code 的拆解发现：**约 98% 是 harness，只有 2% 是模型**。

这个发现量化了「harness 比模型更重要」的直觉判断——模型的推理能力只是整个系统的一小部分，绝大多数复杂度在于上下文管理、工具编排、错误恢复和验证循环。

### Harness 的两个层次

| 层次 | 关注点 | 组件 |
|------|--------|------|
| **单会话 AI 层** | 单次编码会话的质量 | 规则、技能、钩子、子代理 |
| **多代理编排层** | 多个会话的组合工作流 | 任务分解、会话交接、进度追踪 |

### HumanLayer 的「Skill Issues」诊断

引用 HumanLayer 的发现：**大多数代理失败归因于「skill issues」**——即配置问题而非模型权重问题。

这意味着：
1. 模型能力已足够强大，瓶颈在配置
2. 改进方向应优先优化 harness 配置，而非等待更强模型
3. 代理失败是可诊断和可修复的工程问题

### 自动化 Ralph Loop

> 钩子拦截 Agent 的退出意图，重新注入改进 prompt，形成自动化审查循环。

这是 LangChain Terminal Bench 实验中验证有效的 Ralph Wiggum Loop 的通用化描述——通过钩子机制实现自动化质量闭环。

### 实践要点

1. **量化认知**：98% harness 的比例意味着投资 harness 优化的 ROI 远超投资模型选择
2. **两层思维**：单会话质量和多会话编排需要不同的 harness 设计
3. **失败归因**：遇到 Agent 问题时首先检查配置，而非归咎于模型能力
4. **自动化闭环**：Ralph Loop 是可通用的钩子模式，适用于任何编码 Agent

---

## 35. OpenAI Agents SDK 下一代升级：Harness 与计算分离（2026-07-06 更新）

**来源**：[OpenAI - The Next Evolution of the Agents SDK](https://openai.com/index/the-next-evolution-of-the-agents-sdk)

OpenAI 为 Agents SDK 引入了更强大的 model-native harness，标志着 SDK 架构的重大演进。

### 原生沙箱执行（Native Sandbox Execution）

新架构将 harness 与计算层分离：

```
┌───────────────────────────────────────┐
│          Harness Layer (控制平面)       │
│  • Agent 指令和行为边界                  │
│  • 工具编排和 MCP 协议                  │
│  • AGENTS.md 自定义指令                 │
│  • Skills 渐进式披露                    │
└──────────────────┬────────────────────┘
                   │
┌──────────────────┴────────────────────┐
│       Compute Layer (计算平面)          │
│  • 原生沙箱执行环境                      │
│  • 安全性、持久性和可扩展性              │
│  • Shell 和 Apply Patch 工具            │
└───────────────────────────────────────┘
```

### 关键新特性

| 特性 | 说明 |
|------|------|
| **MCP 工具协议** | 原生集成 Model Context Protocol |
| **Skills 渐进式披露** | 按需加载技能，控制上下文窗口 |
| **AGENTS.md** | 通过自定义指令文件定义 Agent 行为 |
| **Shell 工具** | 原生 Shell 命令执行 |
| **Apply Patch 工具** | 结构化代码修改 |

### Harness/计算分离的意义

1. **安全性**：沙箱隔离防止 Agent 越权访问
2. **持久性**：执行状态独立于 harness 配置
3. **可扩展性**：harness 可独立于计算层演进
4. **可移植性**：同一 harness 可适配不同计算后端

### 实践要点

1. **控制/计算分离是架构趋势**：与 Anthropic 的 Managed Agents 架构趋同
2. **MCP 原生集成**：OpenAI SDK 确认 MCP 作为 Agent-Tool 标准协议
3. **AGENTS.md 事实标准**：OpenAI 采纳 AGENTS.md 进一步巩固其作为 Agent 指令标准的地位

---

## 36. Context Engineering 2026 完整实战指南：五大上下文层（2026-07-06 更新）

**来源**：[Taskade - Context Engineering 2026 Complete Guide](https://www.taskade.com/blog/context-engineering/)

### Gartner 宣布 2026 为「The Year of Context」

> 行业调查：**82% 的 IT 和数据领导者认为仅 prompt engineering 已不足以支撑大规模 AI**。

Gartner 正式宣布 2026 年为「上下文之年」，标志行业焦点从提示词优化转向系统化上下文管理。

### 五大上下文层模型

```
┌──────────────────────────────────────┐
│  Layer 5: State (运行状态)            │
│  → Agent 当前处于任务的哪个阶段         │
├──────────────────────────────────────┤
│  Layer 4: Tools (工具)                │
│  → 可用工具和 API（日趋标准化为 MCP）  │
├──────────────────────────────────────┤
│  Layer 3: Memory (记忆)               │
│  → 短期对话 + 长期持久状态              │
├──────────────────────────────────────┤
│  Layer 2: Retrieval (检索)            │
│  → RAG 和外部知识搜索                  │
├──────────────────────────────────────┤
│  Layer 1: Instructions (指令)         │
│  → 系统提示和行为框架                  │
└──────────────────────────────────────┘
```

### 核心论断

> Context engineering 从「如何提问」转向「工程化 AI 模型可访问的数据和上下文」。

这个转变与 Phil Schmid 的权威定义一致：「设计和构建动态系统，在正确的时间、以正确的格式提供正确的信息和工具」。

### 实践要点

1. **State 是被忽视的层**：Agent 需要始终知道自己处于任务的哪个阶段
2. **Memory 分层管理**：短期（会话内）和长期（跨会话）需要不同策略
3. **Tools 标准化**：MCP 正在将工具定义从框架特定变为跨平台标准
4. **Retrieval 精度 > 数量**：返回太多无关结果比不返回更糟

---

## 37. Faros.ai：Harness Engineering 五层成熟度模型（2026-07-07 更新）

**来源**：[Faros.ai - Harness Engineering](https://www.faros.ai/blog/harness-engineering)

### AI 工程成熟度三阶段

Faros.ai 将 Harness Engineering 定义为 AI 工程成熟度的第三阶段：

```
Stage 1: Prompt Engineering  →  写好提示词
Stage 2: Context Engineering  →  管理上下文窗口
Stage 3: Harness Engineering  →  构建完整运行时基础设施
```

### 生产级 Harness 五层架构

| 层级 | 职责 | 关键问题 |
|------|------|----------|
| **工具编排** | Agent 与工具的交互管理 | 工具调用失败时如何恢复？ |
| **验证循环** | Agent 输出的质量检查 | 如何防止错误累积？ |
| **上下文与记忆** | 信息传递和持久化 | Agent 如何记住历史决策？ |
| **护栏** | 安全边界和行为约束 | 如何防止 Agent 越界？ |
| **可观测性** | 监控、日志和追踪 | Agent 做了什么？为什么？ |

### 基线指标建议

Faros.ai 建议工程领导者在投入前先建立基线指标：

- **每个合并 PR 的成本**（算力 + 人工审查时间）
- **Agent 辅助 PR 的合并时间**（vs 纯人工 PR）
- **审查速度**（从 PR 创建到合并的周期）
- **每开发者算力支出**（月度/季度趋势）

### 实践要点

1. **先量化再投入**：不要盲目建设 harness，先建立基线指标确定哪层最需要投入
2. **五层缺一不可**：生产级 harness 需要完整的五层覆盖，缺失任一层都会导致可靠性问题
3. **成熟度演进路径**：从 Prompt 到 Context 到 Harness 是工程化程度递增的路径

---

## 38. Packmind：面向 AI 驱动开发团队的 ContextOps（2026-07-07 更新）

**来源**：[Packmind - Context Engineering Best Practices](https://packmind.com/context-engineering-ai-coding/context-engineering-best-practices)

### 行业现状数据

- **91%** 的工程组织已采用至少一种 AI 编码工具
- **84%** 的开发者使用 AI 工具（Stack Overflow 2025）
- **41%** 的代码由 AI 生成或辅助（Index.dev 2026）
- 但多数团队发现 AI 输出不符合团队约定和架构决策

### ContextOps 概念

> ContextOps = 让 AI 辅助开发在规模上可治理的工程实践

**核心目标**：
- 统一的上下文文件管理（AGENTS.md、CLAUDE.md 等）
- 团队约定和编码标准自动注入 Agent 上下文
- 架构决策记录（ADR）自动传递给 Agent

### 30+ 条可操作实践

Packmind 编译了从入门到高级的 30+ 条实践：

**入门级**：
- 编写第一个有效的上下文文件
- 在 AGENTS.md 中定义项目结构
- 为关键工作流创建 Skills

**进阶级**：
- 构建 ContextOps 基础设施
- 自动化上下文新鲜度检查
- 建立团队级的上下文模板系统

### 实践要点

1. **约定注入是关键痛点**：AI 生成的代码不符合团队规范是最大痛点，需要系统化解决
2. **ContextOps 是治理框架**：不仅仅是技术实践，更是组织级的 AI 编码治理
3. **41% 代码由 AI 生成**：这个比例要求团队重新思考代码审查和架构决策流程

---

## 39. Sourcegraph：Context Engineering 四大支柱（2026-07-07 更新）

**来源**：[Sourcegraph - Context Engineering: A Practical Guide](https://sourcegraph.com/blog/context-engineering)

### 四大支柱模型

| 支柱 | 说明 | 关键技术 |
|------|------|----------|
| **指令 (Instructions)** | 系统提示和行为框架 | 系统提示工程、角色定义 |
| **检索 (Retrieval)** | RAG 和 grounded search | 语义检索、代码库搜索 |
| **记忆 (Memory)** | 短期对话 + 长期持久状态 | 结构化笔记模式、记忆压缩 |
| **可用工具 (Tools)** | 函数调用面，MCP 标准化 | MCP (JSON-RPC 2.0) |

### Anthropic 结构化笔记模式

Sourcegraph 详细介绍了 Anthropic 的结构化笔记模式：
- 模型将草稿本（scratchpad）写到上下文窗口**外**的文件作为持久记忆
- 需要时重新读取这些文件
- 实现跨上下文窗口的信息持久化

### MCP 标准化

> MCP（Model Context Protocol）是连接任意检索源到任意 MCP 兼容客户端的标准化方式（JSON-RPC 2.0）。

- MCP 正在将工具定义从框架特定变为跨平台标准
- Prompt Engineering 仍然重要（写指令和工具描述），但在复杂 Agent 中，提示只是更大上下文管道的一个输入

### 实践要点

1. **四大支柱是完备性检查**：任何 Context Engineering 方案都应覆盖这四个方面
2. **结构化笔记是跨窗口持久化的关键**：将状态写到外部文件 > 依赖上下文窗口
3. **MCP 是标准化连接层**：不是唯一选项，但已成为事实标准
