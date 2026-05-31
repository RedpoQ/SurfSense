# SurfSense CN — Development Changelog

## 2026-05-31 — Phase 0: Project Route Freeze

### 事件

冻结 SurfSense CN 主线项目路线和开发流程。

### 完成事项

- 确认 `D:\SurfSense` 仓库状态（路径、remote、branch、working tree 干净）
- 从 `https://github.com/RedpoQ/SurfSense.git` 克隆仓库至 `D:\SurfSense`
- 只读审计 SurfSense 项目结构、技术栈、架构
- 识别改造点和前置依赖
- 新增项目文档：
  - `docs/PROJECT_ROUTE.md` — 项目路线
  - `docs/FEATURE_MAP.md` — 功能边界
  - `docs/IMPLEMENTATION_BOUNDARY.md` — 实现边界
  - `docs/DEV_WORKFLOW.md` — 开发流程
  - `docs/CHANGELOG_DEV.md` — 本文件

### 未做事项

- 未实现任何业务功能
- 未修改任何 SurfSense 源码（surfsense_backend/、surfsense_web/、surfsense_desktop/ 等）
- 未修改任何配置文件（docker/、.env、pyproject.toml 等）
- 未启动任何服务
- 未安装任何依赖
- 未提交任何代码（本提交为文档冻结）

### 暂停事项

- 暂停 `D:\zhifan\zhifan-learning\` 主线推进
- zhifan-learning 仅作为实验沙盒使用，不承载主线功能

### 关键发现

- SurfSense 已具备完整的文档上传、解析、embedding、RAG 问答能力
- `global_llm_config.example.yaml` 已含 DeepSeek 配置示例（id: -4）
- 前端已有中文 i18n 文件 `messages/zh.json`（811 行）
- 默认 embedding 模型为 `sentence-transformers/all-MiniLM-L6-v2`（英文，384 维），需替换为中文模型
- 后端使用 PostgreSQL pgvector 作为向量数据库（最大 2000 维限制）
- ETL 管道支持三种服务：DOCLING / UNSTRUCTURED / LLAMACLOUD
- 项目版本：0.0.25

### 下一步

Phase 1 — Docker 启动验证：

1. 配置 `docker/.env`（从 `.env.example` 复制，填入 SECRET_KEY）
2. 配置 `global_llm_config.yaml`（DeepSeek API key）
3. 执行 `docker compose up -d`
4. 验证后端 /ready 端点
5. 验证前端可达
