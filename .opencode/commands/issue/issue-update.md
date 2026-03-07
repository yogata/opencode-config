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
4. 次のステップ: `/issue-work` で再実装

### B. Issueへのコメント追加（実装バグ対応）

1. Issue確認: `gh issue view $ISSUE_NUMBER`
2. コメント内容の整理: 問題点、期待される動作、修正方針
3. コメント追加 — テンプレート: `@.opencode/commands/issue/templates/pr_comment_fix.md`
   - テンプレートから作成し、`temp/comment-body.md` に保存
   - コメント追加: `gh issue comment $ISSUE_NUMBER --body-file "temp/comment-body.md"`
4. 次のステップ: `/issue-work` で再実装

## 完了報告

- 本文更新: `✅ Issue #{N} を更新しました。次のステップ: /issue-work {N}`
- コメント追加: `✅ Issue #{N} にコメントを追加しました。次のステップ: /issue-work {N}`

## エラーハンドリング

| エラー            | 対処                                  |
| ----------------- | ------------------------------------- |
| Issueが存在しない | エラー終了。「Issueが見つかりません」 |
| 権限がない        | エラー終了。「編集権限がありません」  |
| 更新内容が空      | エラー終了。「更新内容を入力」        |
