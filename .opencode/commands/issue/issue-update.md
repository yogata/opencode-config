---
description: 既存Issueの本文更新またはコメント追加を行う
---

# Issue更新

既存のIssueに対して、本文の更新またはコメントの追加を行います。主にレビューNG時の「仕様バグ」「実装バグ」対応に使用します。

## 引数

| 引数          | 説明                                                |
| ------------- | --------------------------------------------------- |
| `<Issue番号>`  | 単一:`101` / 複数:`101,102,103` / 省略時は直前のIssue |

## オプション

| オプション  | 説明                        |
| ----------- | --------------------------- |
| `--body`    | Issue本文を更新（デフォルト） |
| `--comment` | Issueにコメントを追加       |

## 手順

### A. Issue本文の更新（仕様バグ対応）

1. Issue確認: `gh issue view $ISSUE_NUMBER`
2. 更新内容の整理: 追加・修正が必要な要件、変更理由
3. Issue本文更新: `gh issue edit $ISSUE_NUMBER --body-file "temp/issue-body.md"`

### B. Issueへのコメント追加（実装バグ対応）

1. Issue確認: `gh issue view $ISSUE_NUMBER`
2. コメント内容の整理: 問題点、期待される動作、修正方針
3. コメント追加 — テンプレート: `@.opencode/commands/issue/templates/pr_comment_fix.md`
   - テンプレートから作成し、`temp/comment-body.md` に保存
   - コメント追加: `gh issue comment $ISSUE_NUMBER --body-file "temp/comment-body.md"`

## 完了時

`@issue-workflow` スキルの「完了報告生成」と「次のステップ提案」を実行してください。

現在のコンテキスト:
- コマンド: issue-update
- Issue番号: {N}
- 更新種別: {body/comment}

## エラーハンドリング

エラーが発生した場合、`@issue-workflow` スキルのエラーハンドリングを呼び出してください。

| 発生しうるエラー | エラーコード |
| ---------------- | ------------- |
| gh認証エラー | `GH_AUTH_ERROR` |
| Issue存在しない | `GH_NOT_FOUND` |
| 権限エラー | `PERMISSION_DENIED` |
| 検証失敗 | `VALIDATION_FAILED` |
