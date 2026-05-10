---
name: obsidian-skill-maintainer
description: Manages creation, update, and organization of Obsidian Vault agent-skills based on vault operational rules. USE FOR: creating new Obsidian skills, updating skills after vault rule changes, reviewing skill inventory, or maintaining skill cross-references. DO NOT USE FOR: general coding tasks, Obsidian note management, or skill quality evaluation.
---

# Obsidian Skill Maintainer

## Overview
Obsidian Vault運用ルールに基づいて、AIエージェント用スキルの作成・更新・整理を管理するメンテナンススキル。

## When to Use
- Obsidian Vaultの運用ルールが変更されたとき
- 新規スキルを作成するとき
- 既存スキルを更新・整理するとき
- スキル間の参照関係を確認・更新するとき

## Directory Structure
```
obsidian-skill-maintainer/
  SKILL.md                    # 本ファイル
  references/
    skill-template.md        # 新規スキル作成用テンプレート
    review-workflow.md       # 運用ルール変更時のレビュー手順
    skill-inventory.md       # 既存スキル一覧
```

## Workflows

### 新規スキル作成
1. `references/skill-template.md` を参照
2. テンプレートに基づきスキルを作成
3. `skill-inventory.md` に追加

### 運用ルール変更時の更新
1. `references/review-workflow.md` に従い手順を実行
2. 影響を受けるスキルを特定
3. 各スキルのreferenceセクションを更新

## References
- `AGENTS.md`: Vault全体の運用ルール
- `40_resources/AGENTS.md`: リソース階層の運用ルール
