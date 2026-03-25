# 实施步骤

## 阶段一：基础设施搭建（Week 1）

### Day 1-2: Agent Runtime 选择

**目标**：选择并配置 Agent Runtime

**选项对比**：

| Runtime | 优势 | 劣势 | 推荐场景 |
|---------|------|------|---------|
| OpenClaw | 完整 Harness 层 | 需要学习曲线 | 企业项目 |
| Claude Code Desktop | 简单易用 | 单 Agent | 个人项目 |
| Codex CLI | OpenAI 生态 | 功能有限 | 快速原型 |

**决策树**：
```
是否需要多 Agent 协作？
├─ 是 → OpenClaw
└─ 否 → 是否需要 UI 验证？
        ├─ 是 → Claude Code Desktop
        └─ 否 → Codex CLI
```

**行动项**：
- [ ] 安装选定的 Runtime
- [ ] 配置基础模型（Claude/GPT）
- [ ] 测试基本功能

---

### Day 3-4: 记忆系统搭建

**目标**：配置长期记忆存储

**推荐方案**：LanceDB + Zhipu Embedding

**步骤**：
1. 安装 memory-lancedb-pro 插件
```bash
openclaw plugin install memory-lancedb-pro
```

2. 配置向量模型
```yaml
# ~/.openclaw/config.yaml
memory:
  provider: lancedb
  embedding:
    provider: zhipu
    model: embedding-3
  llm:
    provider: zhipu
    model: glm-4-flash
```

3. 测试记忆功能
```bash
# 存储记忆
memory_store "项目使用 Vue 3 + TypeScript"

# 检索记忆
memory_recall "Vue 项目配置"
```

**行动项**：
- [ ] 安装向量数据库
- [ ] 配置 Embedding 模型
- [ ] 测试存储和检索

---

### Day 5-7: Git Worktree 配置

**目标**：实现并行开发环境隔离

**步骤**：
1. 创建 worktree 管理脚本
```bash
# scripts/worktree-manager.sh
#!/bin/bash

WORKTREE_BASE="/tmp/worktrees"
BASE_PORT=3000

create_worktree() {
  local branch=$1
  local port_offset=$2
  local worktree_path="$WORKTREE_BASE/$branch"
  
  git worktree add "$worktree_path" -b "$branch"
  
  # 设置端口环境变量
  cat > "$worktree_path/.env.local" << EOF
FRONTEND_PORT=$((BASE_PORT + port_offset * 10))
BACKEND_PORT=$((BASE_PORT + port_offset * 10 + 1))
EOF
  
  echo "✅ Worktree created: $worktree_path"
  echo "   Frontend: http://localhost:$((BASE_PORT + port_offset * 10))"
  echo "   Backend: http://localhost:$((BASE_PORT + port_offset * 10 + 1))"
}

# 使用示例
create_worktree "feature/login" 1
create_worktree "feature/dashboard" 2
```

2. 配置项目支持动态端口
```javascript
// vite.config.js
export default {
  server: {
    port: parseInt(process.env.FRONTEND_PORT || '3000'),
  }
}
```

**行动项**：
- [ ] 创建 worktree 管理脚本
- [ ] 配置项目支持动态端口
- [ ] 测试并行运行

---

## 阶段二：工作流优化（Week 2）

### Day 1-2: 自动化 PR 流程

**目标**：创建 `/git-pr` skill，自动化 PR 创建

**Skill 定义**：
```markdown
# ~/.openclaw/workspace/skills/git-pr/SKILL.md

---
name: git-pr
description: 自动化 PR 创建流程
---

## 功能

1. 分析 git diff
2. 生成 commit message（遵循 Conventional Commits）
3. 生成 PR description
4. 自动提交和推送
5. 创建 PR

## 使用方法

```
/git-pr
```

## 工作流程

1. 执行 `git diff --staged` 获取变更
2. 分析变更类型（feat/fix/refactor/docs）
3. 生成 commit message
4. 执行 `git commit -m "..."`
5. 执行 `git push origin HEAD`
6. 执行 `gh pr create --title "..." --body "..."`
```

**实现脚本**：
```bash
#!/bin/bash
# scripts/git-pr.sh

# 获取变更
DIFF=$(git diff --staged)
STATS=$(git diff --staged --stat)

# 分析变更类型
if echo "$DIFF" | grep -q "feat\|feature"; then
  TYPE="feat"
elif echo "$DIFF" | grep -q "fix\|bug"; then
  TYPE="fix"
else
  TYPE="refactor"
fi

# 生成 commit message（让 AI 完成）
COMMIT_MSG=$(claude "Generate a conventional commit message for this diff:\n$DIFF")

# 提交
git commit -m "$COMMIT_MSG"
git push origin HEAD

# 生成 PR description（让 AI 完成）
PR_BODY=$(claude "Generate a PR description for these changes:\n$STATS")

# 创建 PR
gh pr create --title "$COMMIT_MSG" --body "$PR_BODY"
```

**行动项**：
- [ ] 创建 `/git-pr` skill
- [ ] 配置 Conventional Commits
- [ ] 测试自动化流程

---

### Day 3-4: SWC 配置（如果适用）

**目标**：将编译时间降到 < 1 秒

**步骤**：
1. 安装 SWC
```bash
npm install --save-dev @swc/core @swc/cli
```

2. 配置 `.swcrc`
```json
{
  "jsc": {
    "parser": {
      "syntax": "typescript",
      "tsx": true
    },
    "target": "es2020",
    "loose": false,
    "minify": {
      "compress": true,
      "mangle": true
    }
  },
  "module": {
    "type": "commonjs"
  }
}
```

3. 更新 `package.json`
```json
{
  "scripts": {
    "dev": "swc src -d dist -w",
    "build": "swc src -d dist"
  }
}
```

**Vue 项目注意**：
- Vue 默认用 esbuild，已经很快
- 不需要单独配置 SWC
- 除非有特殊需求

**行动项**：
- [ ] 评估当前编译速度
- [ ] 安装配置 SWC（如果需要）
- [ ] 对比优化前后性能

---

### Day 5-7: UI 自动验证

**目标**：让 Agent 自己验证 UI 变更

**方案 A：Claude Code Desktop（推荐）**
1. 创建 `.claude/launch.json`
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

2. 在 CLAUDE.md 中添加规则
```markdown
## UI 验证规则

1. 任何前端代码变更，必须：
   - 启动 dev server
   - 使用 Preview 功能访问页面
   - 截图确认 UI 符合预期
   - 测试交互功能
   
2. 禁止声明"完成"而未经 UI 验证

3. 发现问题时：
   - 自动修复
   - 重新验证
   - 记录修复过程
```

**方案 B：Playwright 脚本**
```javascript
// scripts/verify-ui.js
const { chromium } = require('playwright');

async function verifyUI(url) {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  
  await page.goto(url);
  await page.screenshot({ path: 'screenshot.png' });
  
  // 检查关键元素
  const errors = [];
  
  // 示例：检查按钮
  const submitButton = await page.$('button[type="submit"]');
  if (!submitButton) {
    errors.push('Submit button not found');
  }
  
  await browser.close();
  
  if (errors.length > 0) {
    throw new Error(errors.join('\n'));
  }
}

verifyUI('http://localhost:3000');
```

**行动项**：
- [ ] 配置 Preview 功能
- [ ] 创建 UI 验证脚本
- [ ] 添加到 CLAUDE.md 规则

---

## 阶段三：并行开发（Week 3）

### 目标：同时运行 5+ 个 Agent

**工作流**：
```
1. 人工规划任务 → 5 个独立功能
2. 启动 5 个 Agents（各在独立 worktree）
3. 每个 Agent 自己验证 UI
4. 人工只做最终 review
5. 合并 PR
```

**配置**：
```yaml
# openclaw 配置
parallelAgents:
  maxConcurrent: 5
  isolation: worktree
  autoVerify: true
```

**监控**：
```bash
# 查看所有运行的 worktrees
git worktree list

# 查看所有运行的进程
ps aux | grep "npm run dev"

# 监控资源使用
htop
```

**行动项**：
- [ ] 创建 5 个 worktrees
- [ ] 启动 5 个并行 Agents
- [ ] 测试资源占用
- [ ] 优化性能

---

## 阶段四：持续优化（Week 4+）

### 每日任务

1. **阅读 harness_engineering_daily 日报**
   - 关注新工具
   - 学习最佳实践
   - 更新本指南

2. **监控性能指标**
   - Agent 执行时间
   - Token 消耗
   - 成功率

3. **收集反馈**
   - 人工介入频率
   - 错误类型
   - 改进机会

### 每周任务

1. **回顾和优化**
   - 分析失败案例
   - 优化配置
   - 更新文档

2. **更新指南**
   - 添加新发现
   - 修正错误
   - 完善案例

---

*更新时间：2026-03-25*
