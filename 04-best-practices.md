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

*更新时间：2026-03-26*
