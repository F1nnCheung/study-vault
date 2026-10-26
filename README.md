# Study Vault

个人知识库仓库。基于 **LLM Wiki** 模式构建：知识在**摄入时**被编译成结构化页面，而不是每次提问时重新检索拼接。

- **人类负责**：挑选来源、决定方向、提好问题、判断意义。
- **Agent 负责**：摘要、交叉引用、归档、一致性维护——所有记账工作。
- **权威规范**：`wiki/AGENTS.md`。本文件只是门厅，冲突时以 `AGENTS.md` 为准。

---

## 目录结构

```
Study Vault/
├── README.md              本文件
├── llm-wiki.md            方法论原文（只读参考）
└── wiki/                  知识库本体
    ├── AGENTS.md          schema：目录含义 / 页面格式 / 工作流，Agent 开工前必读
    ├── index.md           内容目录，按类型分区，每次摄入后更新
    ├── log.md             时序日志，append-only
    ├── overview.md        全局综述：当前论点 / 知识地图 / 主要分歧 / 已知缺口
    ├── raw/               原始来源层 —— 只读，内容永不修改
    │   ├── inbox/           新来源先投这里，待摄入
    │   ├── articles/        网文、博客、剪藏
    │   ├── papers/          论文、报告、白皮书
    │   ├── books/           书籍与章节
    │   ├── notes/           自己的笔记、会议、想法
    │   ├── transcripts/     播客、访谈、讲座转录
    │   └── assets/          图片与 PDF 附件
    ├── sources/           每个来源一页摘要
    ├── entities/          实体：人物、组织、产品、工具、作品
    ├── concepts/          概念：术语、理论、方法、模型
    ├── topics/            主题枢纽页：领域入口
    ├── comparisons/       对比页：选型、权衡、时间线
    ├── answers/           查询结论回填
    └── _templates/        6 份页面模板
```

---

## 三层架构

| 层 | 位置 | 谁写 | 职责 |
|---|---|---|---|
| 原始来源 | `wiki/raw/` | 人 | 事实的唯一来源，不可变 |
| Wiki 正文 | `wiki/` 其余目录 | 仅 Agent | 摘要、实体页、概念页、综合 |
| Schema | `wiki/AGENTS.md` | 人 + Agent 共改 | 让 Agent 成为有纪律的维护者，而非通用聊天机器人 |

正文层的页面全部是 Markdown + YAML frontmatter + Obsidian wikilink，没有数据库，没有向量库。规模到几百页时靠 `index.md` 导航仍然够用。

---

## 三个工作流

### 摄入 Ingest

把来源文件丢进 `wiki/raw/inbox/`，然后对我说「处理一下 inbox」。Agent 会：读全文 → 对照 `index.md` 和 `overview.md` 定位 → 建 `sources/` 摘要页 → 更新周边 5–15 个页面 → 归档到 `raw/` 对应子目录 → 更新 `index.md`、`overview.md`、`log.md`。

一次摄入触及十几个页面是常态，这是设计意图，不是失控。

### 提问 Query

直接问。Agent 读索引、读页面、跨页综合，答案里的每个论断都要能指回某一页。**值得留存的答案会回填到 `answers/`**——探索本身也应该沉淀，不该消失在聊天记录里。

### 体检 Lint

定期说「体检一下」。检查：页面间矛盾、被新来源推翻却仍标 `stable` 的结论、孤儿页、反复提及却没有建页的概念、断链、`index.md` 与磁盘不一致。

---

## 快速开始

```bash
# 1. 投递第一个来源
cp ~/Downloads/some-article.md wiki/raw/inbox/

# 2. 让 Agent 摄入
#    「读 wiki/raw/inbox/some-article.md，按 AGENTS.md 的流程摄入」

# 3. 看结果
#    wiki/sources/ 下新增摘要页，index.md 与 log.md 已更新
```

**建议第一次只摄入一个来源，全程参与。** 走完一遍之后改 `AGENTS.md`——规范是在用起来之后才定型的，当前这版是通用默认值。

---

## 约定速查

| 项 | 规则 |
|---|---|
| 文件名 | 英文 kebab-case，全小写，如 `concepts/scaling-law.md` |
| 标题 | 中文，写在 frontmatter `title`；别名进 `aliases` |
| 链接 | Obsidian wikilink `[[slug]]`，全库唯一时省略路径 |
| 新页面 | 至少 2 个入链、2 个出链，否则等于没写 |
| 改结论 | 不静默删除。旧结论保留并标注被谁取代 |
| 日志条目 | `## [YYYY-MM-DD] 动作 \| 对象`，`grep "^## \[" wiki/log.md \| tail -5` |

---

## 可选配置

**Obsidian**（推荐）：`Open folder as vault` 指向本目录。然后在设置里做两件事：
- `Files and links → Attachment folder path` 设为 `wiki/raw/assets`
- `Hotkeys → 搜索 "Download"`，给「Download attachments for current file」绑个快捷键，剪藏后一键把图片落地

**版本控制**：本目录已是 git 仓库。每次摄入后 commit，误改可回滚，也留下知识库演化史。

```bash
git add -A && git commit -m "ingest: 文章标题"
```

**规模上去之后**：库超过几百页时，可以接 [qmd](https://github.com/tobi/qmd) 做本地混合检索（BM25 + 向量 + LLM 重排），同时提供 CLI 和 MCP server。现在的 `index.md` 方案在中等规模下够用，先不引入复杂度。

---

## 当前状态

空库。目录骨架与 schema 已就位，等待第一个来源。

- 上次更新：2026-10-26（初始化）
- 规模：主题 0 · 概念 0 · 实体 0 · 来源 0 · 对比 0 · 结论 0
