# AGENTS.md — 知识库维护规范

> 本文件是本知识库的 **schema**。任何 LLM/Agent 在读写 `wiki/` 之前必须先读它。
> 它规定目录含义、页面格式、链接方式与三个工作流（Ingest / Query / Lint）。
> 人与 Agent 共同演进这份文件：发现规则不好用就改它，别绕过它。

---

## 0. 一句话

`raw/` 是不可变的原始来源，`sources|entities|concepts|topics|comparisons|answers` 是 Agent 维护的 wiki 正文，`index.md` 与 `log.md` 是导航与时间线。

知识在 **写入时** 被编译成结构化页面，而不是每次提问时重新检索拼接。

---

## 1. 三层架构

| 层 | 位置 | 谁写 | 规则 |
|---|---|---|---|
| 原始来源 | `raw/` | 人（剪藏、下载、投递） | **只读**。Agent 可以读、可以移动文件做归档，但**永不修改内容** |
| Wiki 正文 | `sources/` `entities/` `concepts/` `topics/` `comparisons/` `answers/` `overview.md` | 仅 Agent | Agent 拥有这一层，人只读 |
| Schema | `AGENTS.md`（本文件） | 人 + Agent 共改 | 改动需说明理由 |

树形速查：

```
wiki/
├── AGENTS.md          本文件，schema
├── index.md           内容目录：所有页面的链接 + 一句话摘要（每次 ingest 必更新）
├── log.md             时序日志：append-only，记录 ingest / query / lint
├── overview.md        全局综述：演化中的论点与知识地图
├── raw/               原始来源层（只读）
│   ├── inbox/           新来源先投这里，待处理
│   ├── articles/        网文、博客、剪藏
│   ├── papers/          论文、报告、白皮书
│   ├── books/           书籍与章节
│   ├── notes/           自己的笔记、会议、想法
│   ├── transcripts/     播客、访谈、讲座转录
│   └── assets/          图片、PDF 附件（Obsidian 附件目录指向此处）
├── sources/           每个来源一页摘要页
├── entities/          实体：人物、组织、产品、工具、作品
├── concepts/          概念：术语、理论、方法、模型
├── topics/            主题枢纽页：一个领域的总览与入口
├── comparisons/       对比页：A vs B、方案选型、时间线比较
├── answers/           查询产物回填：分析、连接、结论
├── _templates/        页面模板，复制后填写
└── meta/              方法论与规范原文（如 llm-wiki.md）。只读参考，不产生页面、不进 index.md
```

`meta/` 不参与三层架构，也不是页面类型：它存放「本库为什么这么设计」的原始依据。改 schema 前先回读这里的文档。正文引用写作 `[[meta/llm-wiki|LLM Wiki 方法论]]`。

---

## 2. 页面类型

每种类型对应一个目录，只能放在对应目录里。不要自造类型。

| 类型 | 目录 | 一页是什么 | 例子 |
|---|---|---|---|
| `source` | `sources/` | 一个来源的摘要、要点、它改变了哪些判断 | `sources/attention-is-all-you-need.md` |
| `entity` | `entities/` | 一个具体存在：人/组织/产品/作品 | `entities/andrej-karpathy.md` |
| `concept` | `concepts/` | 一个可复用的抽象：术语/理论/方法 | `concepts/scaling-law.md` |
| `topic` | `topics/` | 一个领域的枢纽页，链接并组织下属页面 | `topics/llm-inference.md` |
| `comparison` | `comparisons/` | 多维对比，结论要写进正文而非散落各处 | `comparisons/rag-vs-wiki.md` |
| `answer` | `answers/` | 一次查询沉淀下来的分析结论 | `answers/why-llm-wiki-beats-rag.md` |

`overview.md` 是唯一的全局页，不属于上述分类，不放进子目录。

---

## 3. 命名与链接

**文件名**：英文 kebab-case slug，全小写，用 `-` 分隔，无空格、无中文、无版本号。
- `concepts/context-window.md` ✅
- `概念/上下文窗口.md` ❌
- 理由：wikilink 解析、grep、后续 CLI 检索（qmd 等）与跨平台同步都依赖 ASCII 路径。

**标题**：中文写在 frontmatter 的 `title` 里，页面一级标题与之一致。中文别名写进 `aliases`，方便检索。

**唯一性**：同一 slug 不得出现在两个目录。若确有歧义，用 `类型-slug` 形式（如 `entities/apple.md` vs `concepts/apple-silicon.md`）。

**链接**：用 Obsidian wikilink，最短唯一路径。
- `[[scaling-law]]` 当且仅当全库唯一
- 否则写全路径 `[[concepts/scaling-law]]`
- 正文首次提到某概念时**必须**链到它的页面；页面不存在就顺手建一个（见 §6 Lint）

**禁止**：`[文字](相对路径.md)` 这类 Markdown 链接（Obsidian 图谱不认）；外链用普通 Markdown 链接。

---

## 4. Frontmatter 规范

每个 wiki 页面必须有 YAML frontmatter。字段如下，不用的字段可省略，但不得改名。

```yaml
---
type: concept            # 必填：source|entity|concept|topic|comparison|answer
title: 扩展定律           # 必填：人类可读标题
aliases: [Scaling Law]   # 可选：别名、英文名、旧称
tags: [llm, training]    # 可选：小写、单数、控制在 3 个以内
status: stable           # 必填：draft|stable|stale|disputed
confidence: high         # 可选：high|medium|low，对结论的把握
sources: [sources/xxx]   # 可选：本页依据的来源页
related: [concepts/yyy]  # 可选：相关页面
created: 2026-10-26      # 必填：YYYY-MM-DD
updated: 2026-10-26      # 必填：任何实质修改都要更新
---
```

`source` 类型额外必填：`raw_path`（指向 `raw/` 下的文件）、`source_type`（article|paper|book|note|transcript）、`author`、`published`。

`status` 的语义：
- `draft` — 刚建，内容不全
- `stable` — 已被足够来源支撑，可以引用
- `stale` — 有新来源推翻了部分内容，等待修订
- `disputed` — 来源之间存在冲突，冲突点必须在正文写清

---

## 5. 工作流

### 5.1 Ingest（摄入）

来源先落到 `raw/inbox/`，然后：

1. **读**。读全文。图片按需单独看（先读文字，再逐张看 `raw/assets/` 下的图）。
2. **对**。读 `index.md` 与 `overview.md`，弄清这条新信息落在知识地图的哪里。
3. **议**（可选但推荐）。向人汇报 3–5 条关键结论，确认侧重。
4. **写摘要页**。在 `sources/` 建一页，中文，结构：
   - 一句话结论
   - 核心要点（3–7 条，每条一句，不堆砌）
   - 与已有知识的**关系**：支持了什么、挑战了什么、补充了什么
   - 存疑之处
5. **改周边**。一次 ingest 触及 5–15 页是常态：
   - 相关 `entities/` `concepts/` 页补事实、补链接
   - 相关 `topics/` 枢纽页更新定位
   - 有冲突 → 相关页 `status: disputed`，并在两页各写对方立场
   - 被推翻 → 旧页 `status: stale`，保留旧结论并标注被谁取代（**不要静默删除**）
6. **归档**。把来源从 `raw/inbox/` 移到对应子目录，`sources/` 页的 `raw_path` 同步更新。
7. **记账**。更新 `index.md` 对应分类；重写 `overview.md` 中受影响段落；向 `log.md` 追加一条。

### 5.2 Query（查询）

1. 读 `index.md` 定位候选页（规模到几百页时这一步仍然够用；再大再上搜索工具）。
2. 读候选页及其一跳链接。
3. 作答：**每个论断都要能指回某个页面**，冲突处必须并列呈现，不许替来源下判断。
4. **回填**。值得留存的答案写进 `answers/`，frontmatter `type: answer`，正文末尾列出「依据页面」。同时在 `index.md` 登记、或在相关页 `related` 里加链。
5. 只值得一句话的结论，直接写进对应的 `concepts/` 或 `topics/` 页，不单开页。

判断标准：**这个问题半年后还会被问第二次吗？** 会 → 回填。

### 5.3 Lint（体检）

周期性执行，输出一份问题清单，逐条修：

- [ ] 矛盾：两页对同一事实结论相反且都没标 `disputed`
- [ ] 过时：新来源已推翻的结论仍标 `stable`
- [ ] 孤儿：无任何入链的页面（除 `index.md` / `overview.md` 外）
- [ ] 缺页：正文反复提到但从未建页的概念/实体
- [ ] 断链：wikilink 指向不存在的页面
- [ ] 空转：`draft` 状态超过 30 天未动的页面
- [ ] 索引漂移：`index.md` 与磁盘实际文件不一致
- [ ] 缺口：本领域明显缺失、可通过一次检索补上的知识

修完向 `log.md` 追加一条 `lint` 记录，列出改了什么。

---

## 6. 写作原则

1. **结论先行**。每页第一段就是这页的答案，不要铺垫。
2. **可追溯**。任何非常识论断，要么链到 `sources/` 页，要么标 `confidence: low`。
3. **不静默改写**。事实变了，保留旧记录 + 标注取代关系。知识库的价值一半在「曾经怎么想」。
4. **一页一主题**。页面超过 ~400 行或开始出现两套无关论点，就拆分。
5. **链接即价值**。写新页时至少给出 2 个入链、2 个出链；孤立页等于没写。
6. **中文正文，英文标识**。正文中文；目录名、文件名、frontmatter 字段名、tag 用英文。
7. **不注水**。宁可 `draft` 留白，也不写没有信息量的过渡句。

---

## 7. 索引与日志格式

**index.md** 按类型分区，每行一条：

```
- [[sources/attention-is-all-you-need|Attention Is All You Need]] — Transformer 原始论文，自注意力取代循环结构（2026-10-26）
```

**log.md** 每条形如 `## [YYYY-MM-DD] 动作 | 对象`，动作限定为 `ingest` / `query` / `lint` / `refactor`：

```
## [2026-10-26] ingest | Attention Is All You Need
```

这样 `grep "^## \[" log.md | tail -5` 就能拿到最近 5 条。

---

## 8. 边界

- **不改** `raw/` 下任何文件内容。可以移动文件做归档，移动后同步 `raw_path`。
- **不造假**。来源里没有的事实、数字、引用，一律不写。缺就写「来源未涉及」。
- **不越权**。`AGENTS.md` 的规则改动要显式说明；不确定就先问人。
- **不删页**。删除只在人明确要求时执行，且先在 `log.md` 记录。
