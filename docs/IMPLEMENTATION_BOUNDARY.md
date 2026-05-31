# SurfSense CN — Implementation Boundary

## 核心原则

### 1. 先找 SurfSense 原有链路

在写任何代码之前，必须先搜索 SurfSense 代码库中是否已有对应功能或可复用的模块。

执行顺序：

```
搜索 → 理解 → 评估 → 决定（配置 / 改造 / 新增）
```

禁止跳过搜索直接实现。

### 2. 优先配置，不重写

SurfSense 的很多能力通过环境变量和 YAML 配置即可启用。优先尝试配置路径：

- LLM provider → `global_llm_config.yaml`
- Embedding → `EMBEDDING_MODEL` 环境变量
- ETL 服务 → `ETL_SERVICE` 环境变量
- Reranker → `RERANKERS_ENABLED` + `RERANKERS_MODEL_NAME`

只有配置无法满足需求时，才进入改造路径。

### 3. 优先改造，不重建

当配置无法满足时，优先修改 SurfSense 现有模块，而非新建平行模块。

改造原则：

- 改现有文件，不新建同功能文件
- 保持原有接口不变
- 新增逻辑通过条件分支（if/else）或策略模式接入
- 删除原有逻辑前必须确认无其他调用方

### 4. 新增模块必须隔离

当 SurfSense 完全没有对应能力时（如错题本、复习建议），才允许新增模块。

隔离规则：

- 新增代码放在独立目录或独立文件中
- 通过明确的接口与 SurfSense 现有系统交互
- 不污染 SurfSense 现有模块的职责边界
- 新增模块必须有独立的测试

---

## 分层约束

每次任务**只能改一层**，禁止跨层混合修改：

| 层 | 英文 | 说明 |
|---|------|------|
| 1 | ingest | 资料上传、文件接收 |
| 2 | parse | 文件解析（ETL 管道） |
| 3 | chunk | 文本分块 |
| 4 | embed | 向量嵌入 |
| 5 | retrieve | 检索（向量搜索 + 全文搜索） |
| 6 | answer | 回答生成（LLM 调用） |
| 7 | citation | 引用标注 |
| 8 | UI | 前端界面 |
| 9 | config | 配置文件 |

禁止一次任务同时修改 `parse` + `embed` + `UI`。

---

## 禁止项

### 禁止一次任务混合多种职责

禁止在同一个任务中同时修改：

- UI + parser
- UI + LLM
- UI + embedding
- parser + database
- LLM + database
- auth + 任何业务逻辑
- deployment + 任何业务逻辑

### 禁止静默修改 API contract

- 不修改现有 API 的请求/响应格式
- 不修改现有 API 的 URL 路径
- 不修改现有 API 的 HTTP 方法
- 如需变更 API，必须在 Task Card 中明确说明并获得审批

### 禁止静默修改数据库 schema

- 不修改现有表结构
- 不新增表
- 不修改 Alembic 迁移文件
- 如需变更数据库，必须在 Task Card 中明确说明并获得审批

### 禁止引入大依赖

- 不新增 Python 包依赖（pyproject.toml）
- 不新增 Node.js 包依赖（package.json）
- 如需引入新依赖，必须在 Task Card 中说明：
  - 依赖名称和版本
  - 引入理由
  - 替代方案分析
  - 包大小和安全审计状态

### 禁止提交 secrets

- API key、密码、token 等敏感信息不得出现在代码中
- 不得提交 `.env` 文件（只提交 `.env.example`）
- 不得在配置文件中硬编码密钥

---

## 改造检查清单

每次改造前，必须回答以下问题：

- [ ] SurfSense 原有链路在哪里？（文件路径 + 行号）
- [ ] 原有链路的能力边界是什么？
- [ ] 我要改的是什么？（配置 / 改造 / 新增）
- [ ] 我改的是哪一层？（只允许一层）
- [ ] 我的修改会影响哪些现有功能？
- [ ] 我是否引入了新的依赖？
- [ ] 我是否修改了 API contract？
- [ ] 我是否修改了数据库 schema？

全部回答后才能开始编码。
