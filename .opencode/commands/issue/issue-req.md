---
description: 要件を整理・定義する（機能追加・バグ修正共通）
---

# 要件定義

機能追加またはバグ修正の要件を整理・定義します。

## 前提

`@issue-workflow` スキルを実行し、パターン（A/B）を判定してください。

## オプション

- `--feature` — 機能追加モード（自動判定）
- `--bug` — バグ修正モード（自動判定）

## 手順

### パターンA（バグ修正）

1. 事象の確認（エラーメッセージ、ログ等）
2. 関連コードの特定（`grep_search`、`codebase_search`）
3. 根本原因の分析
4. 影響範囲の評価

### パターンB（機能追加）

1. 機能の概要と目的を整理
2. 要件の洗い出し（機能/非機能）
3. スコープの定義（対象/対象外）
4. ADR作成要否の判定（`@adr-guidelines` スキル使用）
5. 仕様書更新（承認後）
   - 要件追加: `docs/requirements.md`
   - HLD追加: `docs/specifications.md`
   - ADR作成: `docs/adr/NNN-xxx.md`（スキル判定で「推奨」の場合のみ）

**テンプレート**:

- HLD: `@.opencode/commands/issue/templates/doc_hld.md`
- ADR: `@.opencode/commands/issue/templates/doc_adr.md`

## 出力形式

- パターンA: `@.opencode/commands/issue/templates/issue_comment_bug_analysis.md`
- パターンB: `@.opencode/commands/issue/templates/issue_comment_feature_technical.md`

## 完了時

`@issue-workflow` スキルの「完了報告生成」と「次のステップ提案」を実行してください。

現在のコンテキスト:

- コマンド: issue-req
- パターン: {判定結果}

## エラーハンドリング

`@issue-workflow` スキルのエラーハンドリングを参照してください。
