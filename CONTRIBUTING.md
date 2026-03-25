# 贡献指南

## 文档结构

```
harness-guide/
├── README.md              # 核心概念 + 目录 + 更新日志
├── 01-architecture.md     # 架构设计（三层架构）
├── 02-tools.md           # 工具选型（分类推荐表）
├── 03-implementation.md   # 实施步骤（4 周计划）
├── 04-best-practices.md   # 最佳实践（含 Neil 案例）
├── 05-case-studies.md     # 案例研究（5 个案例）
├── 06-troubleshooting.md  # 故障排查（待完善）
├── 07-reference.md        # 参考资料（待完善）
└── CONTRIBUTING.md        # 本文件
```

## 内容更新规范

### 每日更新流程

```
8:40 AM
    ↓
搜集信息
├─ OpenAI Blog (https://openai.com/index/harness-engineering/)
├─ Anthropic Blog (https://www.anthropic.com/engineering/)
├─ Termdock 博客 (https://www.termdock.com/en/blog)
├─ GitHub 项目（DeerFlow/LangGraph/CrewAI 等）
└─ 社区最佳实践（Medium/Substack/HN）
    ↓
内容分析
├─ 是否填补空白？（新概念/新工具/新案例）
├─ 是否改进现有内容？（更详细/更好示例）
├─ 是否有实践价值？（可落地/可验证）
└─ 是否来自可信来源？（官方/专家/社区）
    ↓
分类更新
├─ 新概念 → 01-architecture.md
├─ 新工具 → 02-tools.md
├─ 实施方法 → 03-implementation.md
├─ 经验总结 → 04-best-practices.md
├─ 成功案例 → 05-case-studies.md
└─ 问题解决 → 06-troubleshooting.md
    ↓
提交 Git
    ↓
发送更新摘要到飞书群
```

### 内容分类标准

| 内容类型 | 目标章节 | 判断标准 |
|---------|---------|---------|
| **新概念** | 01-architecture.md | 新的架构模式/组件/设计原则 |
| **新工具** | 02-tools.md | 新的框架/库/服务，有推荐价值 |
| **实施方法** | 03-implementation.md | 可操作的步骤/配置/脚本 |
| **经验总结** | 04-best-practices.md | 来自实践的经验/技巧/陷阱 |
| **成功案例** | 05-case-studies.md | 真实项目的实施经验 |
| **问题解决** | 06-troubleshooting.md | 常见问题 + 解决方案 |

### Git 提交规范

```bash
git commit -m "docs: update guide with YYYY-MM-DD findings

- Add [new tool/concept] (章节名)
- Update [section] (修改内容)
- Add case study: [name] (案例名)

Refs: 来源1, 来源2, ..."
```

**提交类型**：
- `docs:` - 文档更新
- `feat:` - 新增章节/功能
- `fix:` - 修正错误
- `refactor:` - 重组结构

## 质量控制

### 链接验证

**强制要求**：
- 每个参考链接必须可访问
- 使用 `web_fetch` 或 `agent-browser open` 验证
- 失效链接必须删除或替换

**验证脚本**：
```bash
# 验证单个链接
web_fetch <url>

# 批量验证（示例）
grep -oP 'https?://[^)]+' *.md | while read url; do
  web_fetch "$url" || echo "Failed: $url"
done
```

### 格式检查

- [ ] Markdown 语法正确
- [ ] 表格对齐
- [ ] 代码块有语言标识
- [ ] 链接格式正确 `[文本](URL)`
- [ ] 章节层级合理（不超过 4 级）

### 内容完整性

- [ ] 每个章节有明确标题
- [ ] 工具推荐有推荐理由
- [ ] 代码示例可运行
- [ ] 配置示例有注释

## 版本管理

### 版本标签

```bash
# 每周打一个版本标签
git tag -a v1.0.0 -m "Week 1: Initial framework"
git tag -a v1.1.0 -m "Week 2: Added tool recommendations"
git tag -a v1.2.0 -m "Week 3: Added case studies"
```

### 更新日志

在 `README.md` 中维护更新日志：

```markdown
## 更新日志

### 2026-03-26
**新增**：
- 最佳实践：Neil Kakkar 的 4 阶段工作流
- 工具推荐：Worktree 管理脚本

**修改**：
- 02-tools.md：添加 SWC 详细配置
- 04-best-practices.md：添加摩擦理论

**案例**：
- 05-case-studies.md：完善 Tano 案例
```

## 演化方向

### 短期（1-2 周）
- [ ] 完善故障排查章节
- [ ] 添加更多代码示例
- [ ] 补充配置模板

### 中期（1-2 月）
- [ ] 添加在线预览（GitHub Pages）
- [ ] 建立工具对比矩阵
- [ ] 整理常见问题 FAQ

### 长期（3+ 月）
- [ ] 接受社区 PR
- [ ] 建立讨论区
- [ ] 发布正式版本

## 参考资料

### 必读博客
- [OpenAI Harness Engineering](https://openai.com/index/harness-engineering/)
- [Anthropic Engineering](https://www.anthropic.com/engineering/)
- [Termdock Blog](https://www.termdock.com/en/blog)
- [Phil Schmid](https://www.philschmid.de/)

### 关键项目
- [DeerFlow](https://github.com/bytedance/deer-flow)
- [LangGraph](https://github.com/langchain-ai/langgraph)
- [CrewAI](https://github.com/joaomdmoura/crewAI)
- [OpenAI Agents SDK](https://github.com/openai/openai-agents-python)
- [Google ADK](https://github.com/google/adk-python)

---

*本指南持续更新中，欢迎贡献！*
