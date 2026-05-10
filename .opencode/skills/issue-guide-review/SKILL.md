---
name: issue-guide-review
description: Provides review rejection handling flows and issue-next inference rules for post-review scenarios. USE FOR: handling review NG results, determining next command after review rejection, classifying rejection types (spec-bug, impl-bug, scope-creep), or resolving Epic-related command inference. DO NOT USE FOR: general command execution, requirement analysis, or implementation planning.
---

# Issue Guide Review スキル

issue-*系コマンドのレビューNG時の対応フロー・issue-next推論ルールを提供する。

- **知識ベース**: レビューNG時の対応フロー、issue-next推論ルール
- **参照先**: issue-*コマンドおよびissue-nextから参照される
- **特性**: 宣言的定義のみを提供。手順・手続きは含まない
- **自明な質問の禁止**: エージェントが自律的に判断できることをユーザーに確認しない

---

## レビューNG時の対応フロー

レビュー結果がNGの場合、乖離の種類に応じて対応フローを切り替える。

### NG理由の定義と対応フロー

| NG理由 | 定義 | 対応フロー |
| ------ | ---- | ---------- |
| 仕様バグ | 要件定義と実装の間に論理的矛盾がある | `spec-compliance` 結果確認 → `/issue/issue-update {N} --req --review-ng`（該当REQのUPDATE）→ `/issue/issue-work {N}` |
| 実装バグ | 要件定義は正しいが実装が仕様を満たさない | `spec-compliance` 結果確認 → `/issue/issue-update {N} --comment --review-ng`（レビューNGテンプレート使用）→ `/issue/issue-work {N}` |
| スコープ外逸脱 | 実装が要件定義の範囲を超えている | `/issue/issue-update {N} --req --review-ng`（REQの該当セクションUPDATE）→ 不要な実装を削除 → `/issue/issue-work {N}` |

### `--review-ng` フラグ

`issue-update` に `--review-ng` を付与すると、レビューNG専用テンプレート（`issue_comment_review_ng.md`）を使用してコメントを投稿する。`spec-compliance` の報告内容（影響度、対象、内容、推奨アクション、理由）をテンプレートに反映する。

---

## issue-next レビューNG時推論ルール

`issue-next` コマンドは、レビュー結果から適切な次アクションを推論する。

| 条件 | 推論結果 |
| ---- | -------- |
| レビュー結果に「仕様バグ」が含まれる | `/issue/issue-update {N} --req --review-ng` → `/issue/issue-work {N}` |
| レビュー結果に「実装バグ」が含まれる | `/issue/issue-update {N} --comment --review-ng` → `/issue/issue-work {N}` |
| レビュー結果に「スコープ外逸脱」が含まれる | `/issue/issue-update {N} --req --review-ng` → 不要実装削除 → `/issue/issue-work {N}` |
| レビュー結果がOK | `/issue/issue-close {N}` |

---

## Epic関連の推論ルール

### Epicラベルの判定基準

| ラベル/本文 | 判定 | 説明 |
| ---------- | ---- | ---- |
| `epic` ラベルが付いている | Epic Issue | 親Issueとして機能し、複数の子Issueを持つ |
| 本文に `Parent: #{N}` が含まれる | Child Issue | 親Epic Issue #N の子Issue |

### Epic Issue作成後の推論ルール

`issue-create` でEpic Issueを作成した直後の推論ルール。

| 条件 | 推論結果 |
| ---- | -------- |
| Issueに `epic` ラベルがある AND 子Issue番号が存在する | `/issue/issue-work {child1} {child2} ...` （全ての子Issueを並列実行） |

### 子Issueクローズ後の推論ルール

`issue-close` で子Issueをクローズした直後の推論ルール。

| 条件 | 推論結果 |
| ---- | -------- |
| 子Issueクローズ後、親Epicに未クローズの子が残っている | `/issue/issue-work {next_child}` （次の未クローズ子Issueを実行）または待機 |
| 子Issueクローズ後、親Epicの全ての子がクローズ済み | `/issue/issue-close {epic_number}` （Epic自動クローズ） |
