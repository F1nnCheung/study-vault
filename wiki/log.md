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

---

## [2026-10-26] refactor | 补收件积压检查与跨库仲裁

- Lint 新增「收件积压」检查项（`raw/inbox/` 超过 14 天未处理），`meta/lint.md` 检查表 12 → 13 条。
- 新增 `AGENTS.md` §10「跨库仲裁」：10.1 三类仲裁原则（必须一致／允许分叉／必须登记）／10.2 分叉点映射表（status 五档、页面类型、链接写法、建页阈值）／10.3 搬去 Work 的七项检查清单／10.4 两库结论冲突时的并列记录。
- §10.2 的表格与 Work Vault §10.2 逐字一致，靠手工 diff 维持，**不做共享层**；单边修改视为违规。
- 头部加「本文件只对 Study Vault 有效」提示，避免 Agent 在两个库之间串用规则。

---

## [2026-10-26] refactor | 与 Work 统一目录结构与书写约定

**前提修正**：两库互不隶属、无内容交换需求，不做跨库仲裁。唯一要对齐的是**使用习惯**——同一个概念定义类文件，在两库必须落在同一个目录，写 frontmatter 与链接的方式必须一样。

**删除**：`AGENTS.md` §10「跨库仲裁」整节（10.1 三类原则 / 10.2 分叉映射表 / 10.3 搬运清单 / 10.4 冲突处置）与头部跨库提示。映射表的前提是两库要交换内容，该前提不成立。

**对齐项**（与 Work 完全一致）：

| 项 | 改动 |
|---|---|
| raw 子目录 | 补 `reports/`，八个子目录同序：inbox / articles / papers / reports / books / notes / transcripts / assets |
| 第六类 | `pages/answers/` → `pages/syntheses/`；页面类型 `answer` → `synthesis`（`git mv`，`_templates/answer.md` → `synthesis.md`） |
| 模板 | `_templates/` 六份，与 Work 用同一套文件 |
| status | `draft\|stable\|stale\|disputed` → `draft\|active\|stale\|contested` |
| 链接 | 短链优先（本库不变，Work 改为与本库一致） |
| 建页阈值 | 2 入 2 出 + 400 行 → 至少 1 入链（建议 2 入 2 出）+ 300–800 字 |
| frontmatter | 字段表统一；`active` 语义为「当前成立」，可引用性交由 `confidence` 表达 |
| 章节骨架 | 1 三层架构 / 2 目录 / 3 页面类型 / 4 命名与链接 / 5 Frontmatter / 6 工作流 / 7 严谨性 / 8 写作要求 / 9 索引与日志 / 10 边界，与 Work 同序号同标题 |
| 三个导航文件 | index / overview / lint 的区块结构与 Work 一致 |

**保留（本库特有）**：探索型定位、准入五类排除项的具体措辞、`sources` 页的「与已有知识的关系」结构、Lint 的「缺口」项。

**新增**：Lint 检查项补「概览不同步」「证据空洞」，与 Work 同为 14 项；`overview.md` 增加准入/时效/分歧三条页头规矩。
