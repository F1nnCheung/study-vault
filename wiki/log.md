# 操作日志

> append-only。只追加，不改写历史条目。
> 条目格式：`## [YYYY-MM-DD] 动作 | 对象`，动作为 `ingest` / `query` / `lint` / `refactor`。
> 末尾列出本次触及的页面与关键变更。

---

## [2026-10-26] refactor | 知识库初始化

依据 `llm-wiki.md` 的 LLM Wiki 模式建立三层结构。

**建立目录**：`raw/`（inbox/article/papers/books/notes/transcripts/assets）、`sources/`、`entities/`、`concepts/`、`topics/`、`comparisons/`、`answers/`、`_templates/`
**建立文件**：`AGENTS.md`（schema）、`index.md`（目录）、`overview.md`（综述）、`log.md`（本文件）、6 份页面模板
**待办**：投递第一个来源到 `raw/inbox/` 并执行首次 ingest

---

## [2026-10-26] refactor | 方法论原文移入 meta/

`llm-wiki.md` 从仓库根目录移至 `wiki/meta/llm-wiki.md`，与知识库本体同处一棵树，仓库整体迁移时不会遗漏。

**迁移方式**：`git mv llm-wiki.md wiki/meta/llm-wiki.md`（保留文件历史）
**触及页面**：`AGENTS.md`（树形图新增 `meta/` 并说明其不属三层架构）、`README.md`（目录树同步）
**未改**：本文件上一条记录中的路径属历史事实，按 append-only 原则保留原样
