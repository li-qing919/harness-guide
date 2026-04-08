# 工具选型

## 分类总览

| 类别 | 工具 | 推荐指数 | 适用场景 |
|------|------|---------|----------|
| **Agent Runtime** | OpenClaw | ⭐⭐⭐⭐⭐ | 完整 Harness 平台 |
| | Claude Code Desktop | ⭐⭐⭐⭐ | 单 Agent 开发 |
| | Codex CLI | ⭐⭐⭐ | OpenAI 生态 |
| **Code Editing** | Serena MCP | ⭐⭐⭐⭐⭐ | LSP 语义编辑 |
| | codebase-memory-mcp | ⭐⭐⭐⭐ | 知识图谱 |
| | Playwright MCP | ⭐⭐⭐⭐ | UI 自动化 |
| **Memory System** | LanceDB | ⭐⭐⭐⭐⭐ | 向量数据库 |
| | SQLite | ⭐⭐⭐⭐ | 结构化存储 |
| | Redis | ⭐⭐⭐ | 缓存层 |
| **Version Control** | Git | ⭐⭐⭐⭐⭐ | 必备 |
| | Git Worktree | ⭐⭐⭐⭐⭐ | 并行隔离 |
| | Graphite | ⭐⭐⭐ | PR 管理 |
| **Testing** | Playwright | ⭐⭐⭐⭐⭐ | E2E 测试 |
| | Jest/Vitest | ⭐⭐⭐⭐⭐ | 单元测试 |
| | Faker | ⭐⭐⭐⭐ | 测试数据 |
| **Build Tools** | SWC | ⭐⭐⭐⭐⭐ | 快速编译 |
| | esbuild | ⭐⭐⭐⭐⭐ | 打包工具 |
| | Vite | ⭐⭐⭐⭐⭐ | 开发服务器 |

---

## Agent Runtime

### OpenClaw

**优势**：
- ✅ 完整的 Harness 层实现
- ✅ 支持子代理调度
- ✅ 内置记忆系统
- ✅ Skills 生态
- ✅ 定时任务

**配置示例**：
```yaml
# ~/.openclaw/config.yaml
agents:
  main:
    model: claude-sonnet-4-6
    memory:
      enabled: true
      provider: lancedb
    skills:
      - ~/.openclaw/workspace/skills/agent-browser
      - ~/.openclaw/workspace/skills/github
```

**适用场景**：
- 企业级 Agent 系统
- 多 Agent 协作
- 需要长期记忆的项目

### Claude Code Desktop

**优势**：
- ✅ 内置 Preview 功能
- ✅ 自动 Worktree 隔离
- ✅ Computer Use（macOS）
- ✅ PR 监控

**配置示例**：
```json
// .claude/launch.json
{
  "version": "0.0.1",
  "autoVerify": true,
  "configurations": [
    {
      "name": "frontend",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "port": 3000,
      "autoPort": true
    }
  ]
}
```

**适用场景**：
- 个人开发者
- 前端项目
- 需要 UI 验证

---

## Code Editing Tools

### Serena MCP

**优势**：
- ✅ LSP 语义级编辑
- ✅ 精确定位符号
- ✅ 自动重构
- ✅ 跨文件操作

**使用示例**：
```bash
# 安装
npm install -g serena-mcp

# 配置
# ~/.config/serena/config.json
{
  "languageServers": {
    "typescript": "typescript-language-server --stdio",
    "python": "pylsp",
    "go": "gopls"
  }
}
```

**适用场景**：
- 大型代码库
- 需要精确编辑
- 跨文件重构

### codebase-memory-mcp

**优势**：
- ✅ 知识图谱
- ✅ 代码关系可视化
- ✅ 上下文理解

**适用场景**：
- 新项目上手
- 复杂代码库导航
- 架构理解

---

## Memory System

### LanceDB

**优势**：
- ✅ 高性能向量检索
- ✅ 支持混合查询
- ✅ 无需外部服务
- ✅ 开源免费

**配置示例**：
```python
# 配置向量模型
embedding:
  provider: zhipu
  model: embedding-3
  
# 配置 LLM
llm:
  provider: zhipu
  model: glm-4-flash
```

**适用场景**：
- 长期记忆存储
- 语义检索
- 知识管理

---

## Build Tools

### SWC

**优势**：
- ✅ Rust 实现，极快
- ✅ 替代 Babel/tsc
- ✅ 支持压缩

**性能对比**：
```
Babel:     1.2s
tsc:       3.5s
SWC:       0.05s  ← 快 20-70 倍
```

**配置示例**：
```json
// .swcrc
{
  "jsc": {
    "parser": {
      "syntax": "typescript",
      "tsx": true
    },
    "target": "es2020"
  }
}
```

**适用场景**：
- 大型前端项目
- 需要快速反馈
- 频繁重启开发服务器

---

## Git Worktree

### 为什么需要 Worktree？

**问题**：
- 只能同时处理一个分支
- 切换分支需要 stash
- 端口冲突

**解决方案**：
- 每个工作区独立目录
- 独立端口配置
- 并行开发

**使用示例**：
```bash
# 创建 worktree
git worktree add ../feature-login -b feature/login

# 端口隔离配置
# .env in each worktree
FRONTEND_PORT=3001
BACKEND_PORT=8001
```

**自动化脚本**：
```bash
#!/bin/bash
# worktree-manager.sh

create_worktree() {
  local branch=$1
  local port_offset=$2
  local worktree_path="../$branch"
  
  git worktree add "$worktree_path" -b "$branch"
  
  # 设置端口
  echo "FRONTEND_PORT=$((3000 + port_offset * 10))" >> "$worktree_path/.env"
  echo "BACKEND_PORT=$((8000 + port_offset * 10))" >> "$worktree_path/.env"
  
  echo "✅ Worktree created: $worktree_path"
}

create_worktree "feature/login" 1
create_worktree "feature/dashboard" 2
```

---

## 测试工具

### Playwright

**优势**：
- ✅ 跨浏览器
- ✅ 自动等待
- ✅ 截图/录像
- ✅ 可视化追踪

**配置示例**：
```javascript
// playwright.config.js
module.exports = {
  use: {
    baseURL: 'http://localhost:3000',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
}
```

**AI 集成**：
```javascript
// 让 AI 验证 UI
const { chromium } = require('playwright');

async function verifyUI() {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto('http://localhost:3000');
  await page.screenshot({ path: 'screenshot.png' });
  
  // 检查关键元素
  const button = await page.$('button[data-testid="submit"]');
  if (!button) throw new Error('Submit button not found');
  
  await browser.close();
}
```

---

## 2026-03-28 更新：热门 Agent Harness 框架（最新数据）

### 🚀 顶级框架（按 Star 数排序，2026-03-29 实时数据）

| 框架 | Stars | 日增长 | 总增长 | 特点 | 适用场景 |
|------|-------|--------|--------|------|---------|
| [superpowers](https://github.com/obra/superpowers) | **120,748** | **+1,917** | +8K+ | Agentic Skills 框架 | 所有 AI 编程助手 |
| [deer-flow](https://github.com/bytedance/deer-flow) | **51,257** | **+803** | +5K+ | 字节 SuperAgent | 长时任务研究 |
| [crewAI](https://github.com/crewAIInc/crewAI) | **47,428** | **+48** | +400+ | 多 Agent 协作 | 角色扮演场景 |
| [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) | **42,712** | **+108** | +600+ | AI 驱动开发方法论 | 敏捷开发 |
| [langgraph](https://github.com/langchain-ai/langgraph) | **27,782** | **+71** | +1.1K+ | 图结构 Agent | 复杂工作流 |
| [openai-agents-python](https://github.com/openai/openai-agents-python) | **20,375** | **+14** | +100+ | OpenAI 官方 SDK | OpenAI 生态 |
| [adk-python](https://github.com/google/adk-python) | **18,643** | **+14** | +100+ | Google Agent 工具包 | Google 生态 |

### 🎯 2026年框架发展趋势分析

#### 1. 生态爆发期
- **Superpowers** 突破 118K stars，单周增长 3K+
- **DeerFlow** 接近 50K，字节跳动背书增长强劲
- **BMAD-METHOD** 稳步增长，方法论日趋成熟

#### 2. 架构演进方向
- **三 Agent 架构**：Planner → Generator → Evaluator（Anthropic 模式）
- **插件化生态**：自动注册、跨语言支持、版本管理
- **基础设施标准化**：配置一致性、资源分配策略

#### 3. 企业级特性增强
- **生产环境验证**：字节跳动、Google 等大厂实践
- **安全隔离**：沙箱、权限、资源限制完善
- **运维工具**：监控、日志、故障恢复机制

#### 4. 标准化进程
- **MCP 集成**：Model Context Protocol 成为标准
- **插件市场**：中心化技能分发平台
- **元数据标准**：.claude-plugin 等规范兴起

### 🔥 本周重点关注

#### Superpowers 最新动态：
- **Codex 工具集成增强**：更好的 OpenCode 生态支持
- **插件自动注册**：简化插件部署和发现
- **企业级功能**：完整的监控和管理工具

#### DeerFlow 最新更新：
- **better-auth 服务助手**：身份验证集成
- **CI 工作流优化**：持续集成改进
- **跨语言文档同步**：多语言支持增强

#### BMAD-METHOD 进展：
- **市场重构**：插件市场平台化
- **元数据标准**：.claude-plugin 兼容层
- **工程化工具**：从概念到生产的完整流程

### 新兴 Harness 项目

| 项目 | Stars | 特点 |
|------|-------|------|
| [Trellis](https://github.com/mindfold-ai/Trellis) | 4,179 | 最佳 Agent Harness 设计 |
| [desloppify](https://github.com/peteromallet/desloppify) | 2,614 | 让 slop 代码变优雅 |
| [dexto](https://github.com/truffle-ai/dexto) | 598 | 通用 Agent Harness |
| [moosestack](https://github.com/514-labs/moosestack) | 558 | ClickHouse 分析 Harness |
| [oh-my-agent](https://github.com/first-fluke/oh-my-agent) | 496 | 便携式多 Agent Harness |

### Context Engineering 专项资源

| 项目 | Stars | 特点 |
|------|-------|------|
| [get-shit-done](https://github.com/gsd-build/get-shit-done) | 41K+ | Meta-prompting + Spec-driven |
| [parlant](https://github.com/emcie-co/parlant) | 17K+ | 客户交互控制层 |
| [Agent-Skills-for-Context-Engineering](https://github.com/muratcankoylan/Agent-Skills-for-Context-Engineering) | 14K+ | Context Engineering Skills |
| [context-engineering-intro](https://github.com/coleam00/context-engineering-intro) | 12K+ | Context Engineering 入门 |
| [Context-Engineering](https://github.com/davidkimai/Context-Engineering) | 8K+ | Karpathy 启发的手册 |
| [MineContext](https://github.com/volcengine/MineContext) | 5K+ | 字节 Context Engineering |

---

*更新时间：2026-03-29*

---

## 2026-03-30 框架版本迭代速报

### Superpowers (122,872 ⭐)
- 已进入 Claude 官方插件市场
- 🎯 **核心理念**：Agent 先理解需求 → 生成规格 → 分块展示给用户确认 → 制定实施计划 → 子 Agent 驱动开发
- 支持 Claude Code、Cursor、Codex 和 OpenCode
- 真正的红/绿 TDD 流程

### DeerFlow (52,492 ⭐)
- 🔥 **DeerFlow 2.0 已完成全面重写**（与 v1 无代码共享）
- 定位升级为 **"Super Agent Harness"**
- 2026-02-28 登顶 GitHub Trending 第一名
- 新版本集成：sub-agent 编排、记忆管理、沙箱环境和可扩展技能系统
- 推荐模型：Doubao-Seed-2.0-Code、DeepSeek v3.2 和 Kimi 2.5
- 🐛 近期修复 artifacts API 存储型 XSS 漏洞（CVE）

### LangGraph (27,869 ⭐)
- 被多家独立评测评为 **2026 年最佳 AI Agent 框架**
- 核心特性：持久化执行（Durable Execution）、Human-in-the-loop、综合记忆系统
- LangSmith 调试集成 + 生产级部署支持
- 集成 Deep Agents 和 LangChain 生态系统

### CrewAI (47,510 ⭐)
- 完全独立于 LangChain 的轻量级 Python 框架
- 支持 **Crews**（自主协作）和 **Flows**（企业级事件驱动架构）两种模式
- 已获得超过 **10 万开发者认证**
- 🆕 推出 **AMP Suite 企业套件**：Control Plane、追踪可观测性、统一管理平台、安全合规

### OpenAI Agents SDK (20,402 ⭐)
- 🆕 **Provider-agnostic**：支持 OpenAI Responses/Chat Completions API 及 **100+ 其他 LLM**
- 核心功能：Agent as Tools / Handoffs、MCP 工具支持、Guardrails 安全检查
- Human-in-the-loop、Session 管理和 Realtime Voice Agent 支持

### Google ADK (18,654 ⭐)
- Code-first Python Agent 工具包，优化 Gemini 但模型无关
- 🆕 **近期新增**：
  - 自定义服务注册（Service Registry）
  - Session Rewind（回滚到之前调用状态）
  - Vertex AI Code Execution Sandbox 的 CodeExecutor
  - Agent Config（无代码配置）
  - Tool Confirmation（HITL 工具确认流）

### BMAD-METHOD (42,823 ⭐)
- 🆕 **V6 发布**，定位为 AI 驱动的敏捷开发方法论框架
- 核心特性：
  - 12+ 专项 Agent（PM、架构师、开发者、UX 等）
  - **规模-领域自适应**（自动根据项目复杂度调整规划深度）
  - **Party Mode**（多 Agent 角色协作）
  - Skills Architecture + Dev Loop Automation
- 100% 免费开源

---

## 2026-03-29 框架版本迭代速报

### Superpowers (120,748 ⭐)
- `v5.0.6` (2026-03-25)
- 🎯 **重大改进**：用 Inline Self-Review 替代 Subagent Review Loops
- 📊 执行时间减少约 25 分钟，质量不降
- 目前 star 数最高的 Agent 技能框架和开发方法论项目

### DeerFlow (51,257 ⭐)
- 持续增长，已超 51K ⭐
- 🔧 支持通过 lead_agent 自定义 Channel Assistant ID
- 🐛 修复 MemoryMiddleware 和 task_tool 中 thread_id 回调逻辑

### BMAD-METHOD (42,712 ⭐)
- `v6.2.2` (2026-03-26)
- 📋 module-help CSV 现代化为 13 列格式
- 🔄 使用 `after`/`before` 依赖图替代序列号
- 🔧 重写 bmad-help 执行逻辑

### LangGraph (27,782 ⭐)
- `langgraph-cli==0.4.19` (2026-03-20) — 最新
- 新增 `deploy revisions list` 命令
- 依赖更新和安全修复

### CrewAI (47,428 ⭐)
- `v1.13.0rc1` (2026-03-27) — 即将正式发布
- 继续迭代多 Agent 协作能力

### OpenAI Agents SDK (20,375 ⭐)
- `v0.13.2` (2026-03-26)
- 修复 griffelib 依赖和 private tool metadata 泄露问题

### Google ADK (18,643 ⭐)
- `v2.0.0-alpha.2` (2026-03-27)
- 新增 Agent 名称验证防止任意模块导入
- 修复多个 Bug，向 2.0 正式版推进

---

## 2026-03-28 框架版本迭代速报

### LangGraph (27,711 ⭐)
- `langgraph==1.1.3` (2026-03-18) — 最新稳定版
- `langgraph-cli==0.4.19` (2026-03-20)
- `langgraph-sdk==0.3.12` (2026-03-18)
- 安全修复：cryptography 升级至 46.0.6
- 新增仓库威胁模型文档

### CrewAI (47,387 ⭐)
- `1.13.0rc1` (2026-03-27) — 即将发布的 1.13 正式版
- 重要安全修复：XXE 攻击防护（defusedxml 替换）
- 循环检测 off-by-one 修复
- Bedrock Converse API 工具参数丢失修复

### OpenAI Agents SDK (20,361 ⭐)
- `v0.13.2` (2026-03-26) — 快速迭代中
- 已知问题：base64 文件输入导致 prompt caching 降至 0%
- 功能请求：增强运行生命周期管理（中断和更新活跃运行）

### Google ADK (18,629 ⭐)
- `v2.0.0a2` (alpha, 2026-03-27) — v2 大版本预览
- `v1.28.0` (stable, 2026-03-26) — 当前推荐版本
- 已知问题：MCP 缺少消息级认证；多 agent 启动变慢

### DeerFlow (50,454 ⭐)
- 首次突破 50K stars 🎉
- 活跃 issue：MCP filesystem 路径可见但不可用、Memory 管理功能请求

---

## 2026-04-03 框架版本迭代速报

### Superpowers (132,186 ⭐)
- ⭐ 单日增长 +1,956，突破 **132K** 🚀
- `v5.0.7` (2026-03-31)
- 🆕 新增 GitHub Copilot CLI 支持：SessionStart 上下文注入、环境变量检测
- `v5.0.6` 用内联自审查替代子代理审查循环，消除约 25 分钟开销而不降低质量
- 📌 **启示**：当前最高星标的 Agentic Skills 框架，跨 Agent 平台通用技能定位进一步巩固

### DeerFlow (56,695 ⭐)
- ⭐ 持续增长，逼近 **57K**
- 改进 Python 通道重试和线程类型的可靠性 (#1776)
- 新增文档站点 (#1767，实现 MDX 动态路由文档系统)
- 同步 README 目录结构 (#1774)
- 📌 **启示**：文档基础设施投入是 SuperAgent Harness 走向生产化的标志

### LangGraph (28,265 ⭐)
- `v1.1.4` (2026-03-31)
- 修复递归限制默认哨兵冲突 (#7355)
- 添加 LangSmith 集成元数据 (#7203)
- CLI 更新至 v0.4.19，新增 `deploy revisions list` 命令
- 📌 **启示**：与 DeepAgents 协同推进 Harness Engineering 实践，巩固生产级框架定位

### CrewAI (47,865 ⭐)
- 📦 **v1.13.0 正式发布** (2026-04-02) 🎉
- 新增 RuntimeState RootModel 统一状态序列化
- 事件监听器新增技能和记忆事件遥测
- A2UI 扩展 v0.8/v0.9 支持
- 修复多模态视觉前缀（添加 GPT-5 和 o 系列支持）
- 📌 **启示**：v1.13 正式版标志多 Agent 协作框架进入稳定成熟期

### OpenAI Agents SDK (20,521 ⭐)
- 📦 `v0.13.4` (2026-04-01)
- 修复 AnyLLM 响应重放输入验证 (#2813)
- `v0.13.3` 修复服务器会话跟踪器中的过期 hydrated input IDs
- 接受原始图片上传
- 保持高频迭代（约每日发布）
- 📌 **启示**：高频迭代节奏反映 OpenAI 对 Agent SDK 的持续投入

### Google ADK (18,714 ⭐)
- 📦 `v1.28.1` (2026-04-02)
- 支持 **gemini-3.1-flash-live-preview** 模型的 Live 模式
- 同时推进 `v2.0.0-alpha.2`，添加 Agent 名称验证防止任意模块导入
- 📌 **启示**：v2.0 大版本积极开发中，Live 模式支持推动实时 Agent 场景

### BMAD-METHOD (43,365 ⭐)
- `v6.2.2` (2026-03-26)
- 将模块帮助 CSV 现代化为 13 列格式
- 用 `after`/`before` 依赖图替代序列号 (#2120)
- v6.2.1 新增 CI 质量检查 (#2015)
- 📌 **启示**：敏捷方法论框架的工程化成熟度持续提升

---

## 2026-04-08 框架版本迭代速报

### Superpowers (139,330 ⭐)
- ⭐ 单日增长强劲，突破 **139K** 🚀
- 修复 Discord 邀请链接 (2026-04-06)
- 添加发布公告链接，整合社区部分 (2026-04-02)
- 📌 **启示**：社区运营和发布流程持续规范化，逼近 140K 里程碑

### DeerFlow (59,117 ⭐)
- ⭐ 持续增长突破 **59K**
- `fix(provider)`: 修复流式 Codex 输出当 `response.completed.output` 为空时的保留问题 (#1928, 2026-04-07)
- `fix(backend)`: 改进循环检测，使用稳定键对工具调用进行哈希 (#1911, 2026-04-07)
- `fix(frontend)`: 统一本地设置运行时状态并移除侧边栏布局 (#1879, 2026-04-07)
- 📌 **启示**：循环检测哈希优化是长周期 Agent 自主运行的稳定性关键

### LangGraph (28,647 ⭐)
- ⭐ 稳定增长突破 **28.6K**
- CLI Docker 构建新增原生 uv workspace/lockfile 支持 (#7342, 2026-04-08)
- langgraph 版本从 1.1.2 升至 1.1.5 (#7435, 2026-04-07)
- CLI lockfile 相关发布修复 (#7436, 2026-04-08)
- 📌 **启示**：uv workspace 原生支持提升 Python 依赖管理体验

### CrewAI (48,278 ⭐)
- ⭐ 突破 **48K**
- `refactor`: 使用共享 PRINTER 单例 (2026-04-07)
- `fix`: 移除 FilteredStream stdout/stderr 包装器，解决线程锁定问题 (2026-04-07)
- `chore`: 移除未使用的 flow/config.py (2026-04-07)
- 📌 **启示**：代码清理和线程安全修复反映 v1.13 稳定版后的持续打磨

### OpenAI Agents SDK (20,633 ⭐)
- ⭐ 稳定增长突破 **20.6K**
- `fix`: 加强 SQLAlchemySession 对 SQLite 临时锁的鲁棒性 (#2854, 2026-04-07)
- `docs`: 更新翻译文档页面 (#2853, 2026-04-07)
- `docs(tracing)`: 添加 HoneyHive 到追踪集成列表 (#2851, 2026-04-07)
- 📌 **启示**：可观测性生态扩展（HoneyHive）和 session 存储鲁棒性持续提升

### Google ADK (18,797 ⭐)
- ⭐ 稳定增长逼近 **19K**
- `feat`: 为 ADK Web Server 添加 Trigger 端点 (2026-04-07)
- `docs`: 清理 remote triggers README，移除内部引用 (2026-04-07)
- `docs`: 更新 MCP Toolbox 文档站 URL (2026-04-07)
- 📌 **启示**：Trigger 端点为外部事件驱动的 Agent 编排提供基础设施

### BMAD-METHOD (43,896 ⭐)
- ⭐ 稳定增长突破 **43K**
- `refactor(quick-dev)`: 消除 spec-wip.md 单例模式 (#2214, 2026-04-07)
- `feat(installer)`: 全面改进品牌、版本控制和技能清理 (#2223, 2026-04-07)
- `chore(install)`: 停止默认复制技能提示到 _bmad 目录 (#2182, 2026-04-07)
- 📌 **启示**：installer 品牌化和技能清理优化提升用户首次体验

---

## 2026-04-05 框架版本迭代速报

### Superpowers (135,171 ⭐)
- ⭐ 单日增长 +1,145，突破 **135K** 🚀
- 新增发布公告链接、整合社区板块描述
- 📌 **启示**：社区生态建设与发布流程规范化

### DeerFlow (57,803 ⭐)
- ⭐ 持续增长，逼近 **58K**
- 修复并发线程阻塞问题 (#1839)
- 前端 prompt 附件上传修复 (#1833)
- 阻止不支持的 .app 文件上传 (#1834)
- 📌 **启示**：开源 SuperAgent Harness 的边缘场景健壮性持续提升

### LangGraph (28,419 ⭐)
- ⭐ 稳定增长突破 **28.4K**
- 持续依赖更新（checkpoint、CLI、prebuilt 模块）
- **新增 A2A 协议支持**（通过 LangSmith）
- 与 Google ADK、CrewAI 一同支持 **MCP+A2A 双协议**
- 📌 **启示**：MCP+A2A 双协议支持成为 2026 Agent 互操作性标准

### CrewAI (48,034 ⭐)
- ⭐ 突破 **48K**（仓库已迁移至 crewAIInc 组织）
- 新增 AMP Training Tab 文档指南 (#5083)、依赖管理优化
- 2026 年 3 月添加 **A2A 协议支持**
- 拥有三种 MCP 传输机制（Stdio、SSE、Streamable HTTPS）
- 📌 **启示**：MCP 集成深度在主流框架中领先，A2A 支持完善互操作性

### OpenAI Agents SDK (20,570 ⭐)
- ⭐ 稳定增长
- 新增公共 flush_traces API (#2844)
- 修复 SQLite session 写入的文件锁序列化问题 (#2843)
- 多语言文档更新
- 📌 **启示**：可观测性 API 公开化，session 存储可靠性提升

### Google ADK (18,741 ⭐)
- ⭐ 稳定增长
- 新增 token 缺失时的凭证自动刷新
- SecretManagerClient 区域端点支持
- BashTool 子进程可配置资源限制
- 📌 **启示**：GCP 认证流程和工具安全配置持续完善

### BMAD-METHOD (43,565 ⭐)
- ⭐ 稳定增长
- 新增 BMad Builder 公告横幅、贡献规范文档 (#2207)
- 📌 **启示**：社区贡献规范化，AI 驱动敏捷方法论持续进化

---

## 2026-04-04 框架版本迭代速报

### Superpowers (134,026 ⭐)
- ⭐ 单日增长 +1,840，突破 **134K** 🚀
- 合并 README 发布公告 PR (#1029)
- 新增 Discord 社区详细描述
- 新增 release announcements 链接，整合 Community 部分
- 📌 **启示**：社区建设和文档完善进一步巩固 134K 里程碑

### DeerFlow (57,301 ⭐)
- ⭐ 持续增长突破 **57K**
- 新增 pymupdf4llm PDF 转换器，支持异步卸载和自动降级 (#1727)
- 上传文件时注入文档大纲到 Agent 上下文 (#1738)
- 修复 thread_id 回退逻辑 (#1814)
- 📌 **启示**：文档上下文注入是 Context Engineering 在 SuperAgent 中的前沿实践

### LangGraph (28,356 ⭐)
- 发布 langgraph 1.1.6 (#7407)
- 修复 execution info 补丁问题 (#7406)
- CI 改进：重开 PR 时重新运行 issue 链接检查 (#7403)
- 📌 **启示**：持续稳定迭代，CI 流程优化提升贡献质量

### CrewAI (47,957 ⭐)
- 新增 AMP Training Tab 指南文档 (#5083)
- pyproject.toml 添加 exclude-newer 3 天限制
- 修复 SEO 索引字段导致阿拉伯语页面渲染问题
- 📌 **启示**：v1.13.0 正式版后的文档和国际化完善

### OpenAI Agents SDK (20,551 ⭐)
- 更新翻译文档页面 (#2828)
- 修复 AnyLLM reasoning 提取对 vLLM/any-llm 可迭代 Reasoning 对象的支持 (#2822)
- 同步 examples.md 与当前示例目录 (#2827)
- 📌 **启示**：多 LLM 生态兼容性持续增强

### Google ADK (18,730 ⭐)
- 新增 Vertex AI Session Service 自定义 session ID 功能
- ADK CLI 新增 Easy GCP 支持
- 修复空 events_iterator 赋值问题
- 📌 **启示**：GCP 集成深度和易用性同步提升

### BMAD-METHOD (43,485 ⭐)
- quick-dev 模式修复：为 context list paths 指定 {project-root}/ 锚点 (#2200)
- quick-dev 新增 planning artifact 感知，支持上下文感知的规格说明 (#2185)
- 修复越南语文档翻译保真度问题 (#2192)
- 📌 **启示**：context-aware planning 体现 AI 敏捷方法的持续进化

---

## 2026-04-02 框架版本迭代速报

### Superpowers (130,230 ⭐)
- ⭐ 单日增长 +2,090，突破 **130K** 里程碑 🎉
- `v5.0.7` (2026-03-31)
- 新增 contributor guidelines 以减少低质量 Agent PR
- 为 Agent 面向的 guardrails 添加贡献者指南
- 📌 **启示**：130K stars 标志着社区规模最大的 Agentic Skills 框架地位进一步巩固

### DeerFlow (56,018 ⭐)
- ⭐ 持续增长突破 **56K**
- 修复前端按线程持久化模型选择（#1553）
- 修复飞书文件路径被误判为命令的问题（#1654）
- 修复网关层防止客户端发送 context 时的 400 错误（#1660）
- 📌 **启示**：跨平台兼容性和网关层稳定性是长周期 SuperAgent 的持续优化方向

### LangGraph (28,160 ⭐)
- `v1.1.4` (2026-03-31) — 已在上期速报覆盖
- 修复 URL 和 README 问题，更新 configurable metadata 支持
- 📌 **启示**：图编排框架标杆持续稳定迭代

### CrewAI (47,789 ⭐)
- 📦 `v1.13.0a6` (2026-04-01) — alpha 版本持续迭代
- 新增 **A2UI 扩展**支持 v0.8/v0.9 schema 与文档
- 为 **GPT-5 和 o 系列模型**添加 multimodal vision 前缀支持
- 📌 **启示**：CrewAI 率先适配 GPT-5 和多模态 vision，框架前瞻性布局

### OpenAI Agents SDK (20,494 ⭐)
- 📦 `v0.13.4` (2026-04-01) 发布
- 新增本地 MCP Server 的 **callable approval policies** 支持（#2818）
- 升级 Codex Action 到 1.6 版本
- 📌 **启示**：approval policies 是 Agent 安全控制的重要方向，允许细粒度的工具调用审批

### Google ADK (18,694 ⭐)
- `v2.0.0a2` (2026-03-27) — Alpha 阶段
- 新增 `gemini-3.1-flash-live-preview` 模型的 Live 模式支持
- 重构 Vertex AI Session Service 的事件处理
- 📌 **启示**：2.0 Alpha API 变动较大，生产环境建议仍使用 v1.x

### BMAD-METHOD (43,229 ⭐)
- `v6.2.2` (2026-03-26)
- 新增 bmad-checkpoint-preview skill（#2145）
- 修复 adversarial triage 审查问题
- 移除 Barry quick-flow-solo-dev agent
- 📌 **启示**：checkpoint 预览技能增强了开发流程的可视化能力

---

## 2026-04-01 框架版本迭代速报

### Superpowers (128,140 ⭐)
- ⭐ 单日增长 +2,524，持续高速增长
- `v5.0.7` (2026-03-31)
- 🆕 **新增 GitHub Copilot CLI 支持**：SessionStart 上下文注入、Claude Code 到 Copilot CLI 的工具映射表
- 📌 **启示**：跨编码 Agent 通用技能框架的定位进一步强化，不再绑定单一 Agent 平台

### DeerFlow (55,237 ⭐)
- ⭐ 持续增长突破 **55K**
- 🔧 修复 Sandbox 并发执行命令的序列化问题（#1435）
- 🔧 修复 Sandbox 挂载路径的跨平台兼容性（#1638, #1634）
- 📌 **启示**：长周期 SuperAgent Harness 的 Sandbox 稳定性是生产化关键

### LangGraph (28,068 ⭐)
- 📦 `v1.1.4` (2026-03-31) 发布
- 修复递归限制默认哨兵值冲突
- 新增 LangSmith 集成元数据
- 依赖升级：Pygments、cryptography 等
- 📌 **启示**：持续强化与 LangSmith 可观测性的集成，巩固生产级框架定位

### CrewAI (47,726 ⭐)
- 📦 `v1.13.0a4` (2026-03-31) alpha 版
- 稳定版 `v1.12.2` 于 3/26 发布
- 📌 **启示**：v1.13 稳定版即将到来，预发布迭代活跃

### OpenAI Agents SDK (20,463 ⭐)
- 📦 `v0.13.3` (2026-03-31) 发布
- 修复 server conversation tracker 中过期的 hydrated input IDs
- 修复 chat completions 输入对 raw image_url content parts 的支持
- 新增非 OpenAI Provider 代码示例文档
- 📌 **启示**：服务端会话管理和多模态输入兼容性是近期重点

### Google ADK (18,687 ⭐)
- 修复了 Agent 名称验证以防止任意模块导入的安全漏洞
- 修复 SSE 连接问题
- 📌 **启示**：安全加固是 2.0 Alpha 阶段的重点

### BMAD-METHOD (43,099 ⭐)
- `v6.2.2` 持续优化
- 重构模块帮助 CSV 为 13 列格式引入 after/before 依赖图
- 重写 bmad-help 为基于结果的技能设计（缩短约 50%）
- 📌 **启示**：模块化和技能设计的持续优化

---

## 2026-03-31 框架版本迭代速报

### Superpowers (125,616 ⭐)
- ⭐ 突破 **125K** stars 里程碑
- `v5.0.6` (2026-03-25)
- 内联 self-review 和 brainstorm server 重构
- 修复跨平台 owner-PID 生命周期监控
- 修复 owner 以不同用户运行时的误报问题

### DeerFlow (54,031 ⭐)
- ⭐ 持续增长突破 **54K**
- 修复前端 agent 检查路由至 gateway (#1572)
- 优化 Docker 构建使用镜像加速 (#1600)
- 修复开发环境 venv 同步问题 (#1626)

### LangGraph (27,963 ⭐)
- 持续依赖更新（pygments 2.19.2 → 2.20.0）
- 涉及 checkpoint-postgres、sdk-py、checkpoint-sqlite 等多个子模块
- `cli==0.4.19` (2026-03-20)

### CrewAI (47,617 ⭐)
- `v1.13.0rc1` (2026-03-27) — 即将正式发布
- 重构类型注解（用普通 type 替代 InstanceOf[T]）
- 移除未使用的第三方 LLM 目录
- 将 LLM 类转换为 Pydantic BaseModel
- 大规模代码重构为 1.13 稳定版做准备

### OpenAI Agents SDK (20,426 ⭐)
- `v0.13.2` (2026-03-26)
- 修复 server conversation tracker 中过期的 hydrated input ID (#2800)
- 修复 chat completions 输入对 raw image_url 内容的兼容性 (#2799)
- 新增 Codex Stop hook 用于针对性 Ruff 整理 (#2795)

### Google ADK (18,672 ⭐)
- `v2.0.0a2` (2026-03-27) — Alpha
- 🔥 **重大更新**：ADK 2.0 Alpha 引入确定性工作流和图式编排
- 新增 LocalEnvironment 用于本地命令执行和文件 I/O
- 新增 EnvironmentToolset 支持
- 支持 A2A 协议和 MCP 集成

### BMAD-METHOD (42,981 ⭐)
- `v6.2.2` (2026-03-26)
- 新增法语翻译输出路径解析文档 (#2140)
- quick-dev 模式 one-shot 路由生成 spec trace 文件 (#2121)
- 修复 party-mode 下 solo 模式响应展示 (#2164)
