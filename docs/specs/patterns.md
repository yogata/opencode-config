# Implementation Patterns

## コマンドfrontmatter規約

### エージェント指定

コマンドのfrontmatterでagentを指定。対話系コマンド（issue-req）は `agent: prometheus`、ファイル操作系コマンド（issue-save-req, issue-create等）は `agent: sisyphus` を使用。

**対話系コマンド（issue-req）:**
```yaml
---
description: ...
agent: prometheus
load_skills:
  - ...
---
```

**ファイル操作系コマンド（issue-save-req, issue-create等）:**
```yaml
---
description: ...
agent: sisyphus
load_skills:
  - ...
---
```

**理由**: デフォルトエージェント（Plan/Prometheus）の誤用を防止するため。PlanエージェントはRead-only権限であり、ファイル書込やコマンド実行ができない。

## .sisyphus/ 命名規則

`.sisyphus/` 配下の7カテゴリ（plans, drafts, evidence, execution, notepads, tasks, reports）のファイル・ディレクトリ命名は plan 名を基準とする。詳細なルール・例は `AGENTS.md` の「Sisyphus 命名規則」セクションを参照。

**重要**: notepads は完全一致マッチングのみ対応。plan 名にサフィックスがある場合、notepad ディレクトリ名にも同一サフィックスが必要。

## REQ frontmatter規約

REQ文書のfrontmatterには以下のフィールドを定義する:

```yaml
---
id: REQ-{NNNN}
title: {要件タイトル}
status: draft | active | implemented | deprecated
created: {YYYY-MM-DD}
updated: {YYYY-MM-DD}
tags: [{tag1}, {tag2}]
scale: standard | large
---
```

### scale フィールド

- `standard`: 通常のPattern B（デフォルト）。1 REQ → 1 Issue
- `large`: Epic規模。1 REQ → Epic + N子Issue
- 省略時は `standard` として扱う

### draft-meta（issue-req → issue-save-req 引き継ぎ）

issue-req で生成されるドラフトの `draft-meta` セクションに以下を記録する:

```markdown
## draft-meta（issue-save-req 用）

- **pattern**: B
- **scale**: standard | large
- **decomposition**: [{scope, modules, description}]（scale が large の場合のみ）
```
