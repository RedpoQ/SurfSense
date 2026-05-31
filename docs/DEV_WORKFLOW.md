# SurfSense CN — Development Workflow

## 固定流程

每个开发任务必须按以下 9 步执行，不得跳步：

```
1. Repository Check
2. Route Fit Check
3. Existing System Mapping
4. Task Card
5. Implementation
6. Verification
7. Report
8. Human Review
9. Next Task
```

---

## 1. Repository Check

每次任务开始前，必须执行：

```bash
cd D:\SurfSense
pwd
git status --short
git remote -v
git branch --show-current
```

**停止条件：**

- 当前路径不是 `D:\SurfSense` → 停止
- working tree 不干净（有未提交的修改）→ 停止
- remote 不是 `https://github.com/RedpoQ/SurfSense.git` → 停止
- 不在 `main` 分支 → 停止或切回 main

---

## 2. Route Fit Check

确认当前任务符合项目路线：

- 任务是否在 `FEATURE_MAP.md` 的允许开发范围内？
- 当前阶段是否允许开发该模块？
- 任务是否涉及 `IMPLEMENTATION_BOUNDARY.md` 中的禁止项？

**停止条件：**

- 任务不在 Feature Map 中 → 停止，先更新 Feature Map
- 当前阶段不允许开发该模块 → 停止
- 任务违反实现边界 → 停止，重新定义任务范围

---

## 3. Existing System Mapping

在写任何代码之前，搜索 SurfSense 代码库：

```bash
# 搜索相关关键词
grep -r "关键词" surfsense_backend/app/
grep -r "关键词" surfsense_web/

# 查看相关目录
ls surfsense_backend/app/相关目录/
```

必须回答：

- SurfSense 中是否已有对应功能？
- 如果有，文件路径和入口函数是什么？
- 如果有，能力边界是什么？
- 如果没有，需要新增什么？

---

## 4. Task Card

每个任务必须有明确的 Task Card。模板如下：

```markdown
## Task Card

**ID:** TASK-YYYY-MM-DD-NNN
**标题:** <简短描述>
**目标:** <一句话说明要达成什么>
**修改层:** <ingest / parse / chunk / embed / retrieve / answer / citation / UI / config>
**修改文件:** <列出所有要修改的文件路径>
**新增文件:** <列出所有要新增的文件路径>
**新增依赖:** <无 / 列出依赖名和版本>
**API 变更:** <无 / 说明变更>
**数据库变更:** <无 / 说明变更>
**前置依赖:** <需要先完成哪些任务>
**验收标准:** <可验证的完成标准>
**风险:** <可能的风险>
```

---

## 5. Implementation

按 Task Card 执行。执行过程中：

- 只修改 Task Card 中列出的文件
- 不得超出修改层的范围
- 遇到意外情况（需要改更多文件、需要新依赖等）→ 停止，更新 Task Card
- 每个文件修改后，确认修改内容符合预期

---

## 6. Verification

修改完成后，必须执行验证：

```bash
# 1. 确认只修改了 docs 文件（Phase 0）
git status --short
git diff --stat
git diff --check

# 2. 检查是否有意外修改
git diff --name-only

# 3. 如果改了代码，运行测试（Phase 1+）
# cd surfsense_backend && python -m pytest
# cd surfsense_web && pnpm test:e2e
```

**Verification Gate：**

- `git status` 只显示预期修改的文件 → 通过
- `git diff --check` 无 whitespace error → 通过
- 无意外文件被修改 → 通过
- 测试通过（Phase 1+）→ 通过

任一项不通过 → 回到 Implementation 修复。

---

## 7. Report

每个任务完成后，输出报告。模板如下：

```markdown
## Task Report

**ID:** TASK-YYYY-MM-DD-NNN
**状态:** 完成 / 部分完成 / 失败

### Repository Check
- path: D:\SurfSense
- remote: https://github.com/RedpoQ/SurfSense.git
- branch: main
- working tree: 干净 / 有未提交修改

### Changed Files
- <文件路径>: <修改说明>
- <文件路径>: <修改说明>

### What Was Done
<具体做了什么>

### What Was NOT Done
<没有做什么，为什么>

### Verification
- git status: ✅ / ❌
- git diff --stat: ✅ / ❌
- git diff --check: ✅ / ❌
- 测试: ✅ / ❌ / N/A

### Risks Identified
<本次修改引入的风险>

### Next Safe Step
<下一步最小动作>
```

---

## 8. Human Review

报告输出后，等待人工审核。审核内容：

- 修改是否符合预期？
- 是否有遗漏？
- 是否有风险需要处理？
- 是否可以提交？

审核通过后，进入提交流程。

---

## 9. Next Task

当前任务完成后，根据 Feature Map 和项目进展，确定下一个任务。回到步骤 1 开始新的循环。

---

## Commit 规则

### 提交前检查

```bash
git status --short
git diff --stat
git diff --check
```

确认无意外文件、无 whitespace error。

### 提交信息格式

```
<type>(<scope>): <subject>

类型：
- docs: 文档
- feat: 新功能（Phase 1+）
- fix: 修复（Phase 1+）
- refactor: 重构（Phase 1+）
- config: 配置变更
- test: 测试

范围：
- backend: 后端
- web: 前端
- infra: 基础设施
- project: 项目管理

示例：
docs(project): freeze project route and development workflow
feat(backend): configure DeepSeek LLM provider
fix(web): correct Chinese translation in zh.json
config(infra): add bge-large-zh embedding model
```

### 分支策略

- 主线开发在 `main` 分支进行
- 大功能开发从 `main` 创建 feature 分支：`feat/cn-<功能名>`
- 合并前必须通过 Verification Gate

---

## Stop Conditions

以下情况必须**立即停止**当前任务：

1. **Repository Check 失败** — 路径、remote、branch、working tree 任一不符
2. **Route Fit Check 失败** — 任务不符合项目路线
3. **越界修改** — 修改了 Task Card 之外的文件
4. **静默变更** — 意外修改了 API contract、数据库 schema 或引入了新依赖
5. **测试失败** — Verification 阶段测试不通过
6. **不确定** — 对修改的影响范围不确定时，停下来先确认
