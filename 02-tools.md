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

### 🚀 顶级框架（按 Star 数排序，2026-03-28 实时数据）

| 框架 | Stars | 日增长 | 总增长 | 特点 | 适用场景 |
|------|-------|--------|--------|------|---------|
| [superpowers](https://github.com/obra/superpowers) | **118,831** | **+3,151** | +6K+ | Agentic Skills 框架 | 所有 AI 编程助手 |
| [deer-flow](https://github.com/bytedance/deer-flow) | **50,324** | **+2,023** | +4K+ | 字节 SuperAgent | 长时任务研究 |
| [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) | **42,604** | **+172** | +500+ | AI 驱动开发方法论 | 敏捷开发 |
| [crewAI](https://github.com/crewAIInc/crewAI) | **47,380** | **+78** | +300+ | 多 Agent 协作 | 角色扮演场景 |
| [langgraph](https://github.com/langchain-ai/langgraph) | **27,702** | **+115** | +1K+ | 图结构 Agent | 复杂工作流 |
| [openai-agents-python](https://github.com/openai/openai-agents-python) | **20,361** | **+21** | +100+ | OpenAI 官方 SDK | OpenAI 生态 |
| [adk-python](https://github.com/google/adk-python) | **18,628** | **+19** | +100+ | Google Agent 工具包 | Google 生态 |

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

*更新时间：2026-03-28*

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
