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

---

## [2026-10-26] refactor | 补严谨性条款与持久化体检报告

明确本库定位「探索型但不宽松」：探索只针对选题与提问方式，不针对事实。

**新增**：`AGENTS.md` §6「严谨性：四条硬约束」——6.1 准入（什么不进库，五类明确排除）／6.2 存疑与相悖（必须当次上报 + 四种处置动作）／6.3 时效（陈旧信号 + 时间基准 + `review` 复核 + 更新不覆盖）／6.4 置信分级。
**新增文件**：`meta/lint.md`——体检报告改为持久化覆盖写，11 项检查 + 问题清单 + 已修／待修。
**改动**：§5.1 Ingest 由 7 步扩为 9 步（新增「筛」按准入过滤、「报」按 §6.2 上报）；§5.2 Query 要求存疑点在回答中显式标出；§5.3 Lint 产出改为 `meta/lint.md` 与 `log.md` 双写并补触发条件；§4 frontmatter 新增可选字段 `review`；章节编号 6–8 顺延为 7–9；修正 §3 指向 Lint 的失效章节号。
**未变**：目录结构、短链写法、三层架构、`_templates/` 六份模板。

---

## [2026-10-26] refactor | 知识层收口进 `pages/`

原布局把六个类型目录与 `raw/`、`meta/`、`_templates/` 平铺在 `wiki/` 下，知识层与基础设施同层，新增 `raw/` 子目录时有撞名风险。改为单层收口：

**迁移**：`git mv` 将 `sources/` `entities/` `concepts/` `topics/` `comparisons/` `answers/` 移入新建的 `pages/`（空库，零页面，成本为零）。`overview.md` 留在 `wiki/` 根——它汇总全库，不属于任何单类型。

**未变（重要）**：链接规则仍是「最短唯一路径」。路径深了一层，但 `[[scaling-law]]` 照样有效；只有需要消歧时才写 `[[pages/concepts/scaling-law]]`。这是本库区别于 Work 库（强制带路径）的定位特征，靠「slug 全库唯一」这条规则支撑。

**同步修改**：`AGENTS.md`——§0 一句话、§1 架构表与树形图（新增 `pages/` 层级并说明收口理由）、§2 类型表路径、§3 文件名示例与链接规则、§4 `sources`/`related` 字段值、§5.1 与 §5.2 建页路径、§5.3 新增「错位」检查项、§7 可追溯目标、§8 索引条目示例。
**同步修改**：`README.md` 目录树与三层架构表、工作流示例路径、约定速查表；`index.md` 六个分区标注所属目录；`_templates/` 五份模板与 `overview.md` 的占位链接加 `pages/` 前缀；`meta/lint.md` 检查项增至 12 条。

**未改**：`raw/` 结构（仍含 `inbox/` 与 `transcripts/`）、`meta/`、日志历史条目。
