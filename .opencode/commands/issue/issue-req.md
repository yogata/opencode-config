---
description: 要件を整理・定義する（機能追加・バグ修正共通）
---

# 要件定義

機能追加またはバグ修正の要件を整理・定義します。文脈（機能追加/バグ修正）と規模（パターンA/B）を自動判定します。

## オプション

| オプション  | 説明                       |
| ----------- | -------------------------- |
| `--feature` | 機能追加モード（自動判定） |
| `--bug`     | バグ修正モード（自動判定） |

## 規模判定

| パターン    | 規模               | docs/更新 | ADR        |
| ----------- | ------------------ | --------- | ---------- |
| **A（小）** | バグ修正・軽微変更 | なし      | なし       |
| **B（中）** | 新機能追加         | あり      | スキル判定 |

**自動判定キーワード**:
- パターンA: 修正、変更、バグ、エラー、直したい
- パターンB: 追加、新規、作成、実装、機能

## 手順

### パターンA（バグ修正）

1. 事象の確認（エラーメッセージ、ログ等）
2. 関連コードの特定（`grep_search`、`codebase_search`）
3. 根本原因の分析
4. 影響範囲の評価
5. 分析結果の報告（テンプレート参照）

### パターンB（機能追加）

1. 機能の概要と目的を整理
2. 要件の洗い出し（機能/非機能）
3. スコープの定義（対象/対象外）
4. ADR作成要否の判定（`adr-guidelines` スキル使用）
5. 定義内容の報告と更新確認
6. 仕様書更新（承認後）
   - 要件追加: `docs/requirements.md`
   - HLD追加: `docs/specifications.md`
   - ADR作成: `docs/adr/NNN-xxx.md`（スキル判定で「推奨」の場合のみ）

**テンプレート**:
- HLD: `@.opencode/commands/issue/templates/doc_hld.md`
- ADR: `@.opencode/commands/issue/templates/doc_adr.md`

## 出力形式

- パターンA: `@.opencode/commands/issue/templates/issue_comment_bug_analysis.md`
- パターンB: `@.opencode/commands/issue/templates/issue_comment_feature_technical.md`

> ✅ パターンX（規模）と判定しました。次のステップ: `/issue-create`
