# SurfSense CN — Project Route

## 主线仓库

- **路径：** `D:\SurfSense`
- **远程：** `https://github.com/RedpoQ/SurfSense.git`
- **分支：** `main`
- **上游：** `MODSetter/SurfSense`（原始开源项目）

## 定位

**SurfSense CN fork** — 基于 SurfSense 做中文学习化改造。

核心原则：

- **不是从零重写 NotebookLM。** SurfSense 已经是一个功能完整的开源知识库平台，我们站在它的肩膀上做改造。
- **D:\zhifan / zhifan-learning 只是实验沙盒。** 用于早期探索和原型验证，不承载主线业务功能。主线开发全部在 `D:\SurfSense` 进行。
- **改造优先于重建。** SurfSense 已有的能力（文档上传、解析、embedding、向量搜索、RAG 问答、Agent 架构）应当被复用，而非重新发明。

## 目标

将 SurfSense 改造为面向中国学生的**学习增强平台**：

1. 中文文档（教材、论文、笔记）高质量解析与问答
2. 中文 LLM（DeepSeek / Qwen）无缝接入
3. 中文 embedding（bge-large-zh）替代英文默认模型
4. 学习增强模块：重点提取、出题练习、错题本、复习建议

## 当前阶段

**Phase 0：路线冻结与架构审计**

Phase 0 的目标：

- 冻结项目路线和功能边界
- 建立开发流程规范
- 完成 SurfSense 原生能力的只读审计
- 识别改造点和前置依赖

Phase 0 的产出：

- `PROJECT_ROUTE.md`（本文件）
- `FEATURE_MAP.md`
- `IMPLEMENTATION_BOUNDARY.md`
- `DEV_WORKFLOW.md`
- `CHANGELOG_DEV.md`

Phase 0 的禁止项：

- 不写业务代码
- 不改 SurfSense 源码
- 不安装依赖
- 不启动服务
- 不继续 zhifan-learning 主线功能

## 与 zhifan-learning 的关系

- `D:\zhifan\zhifan-learning\` 是**实验沙盒**，仅用于：
  - 验证某个技术方案是否可行（如中文 PDF 解析效果）
  - 测试某个 API 是否正常工作
  - 原型验证某个新模块的接口设计
- **禁止**在 zhifan-learning 上堆砌主线功能
- **禁止**将 zhifan-learning 作为生产项目推进
- 验证完成后的结论应沉淀回 `D:\SurfSense` 的正式实现中
