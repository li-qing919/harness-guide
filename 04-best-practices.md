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

*更新时间：2026-03-31*
