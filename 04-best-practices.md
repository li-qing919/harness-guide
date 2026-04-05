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
