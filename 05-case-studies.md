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

*更新时间：2026-03-29*

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
