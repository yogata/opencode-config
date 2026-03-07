---
description: PRをマージし、対応記録を追記し、Issueをクローズしてブランチを削除する
---

# 完了処理

PRをマージし、GitHub Issueに記録を追記し、クローズ後にworktreeとブランチを削除します。

## 引数

| 引数          | 説明                                                |
| ------------- | --------------------------------------------------- |
| `<Issue番号>`  | 単一:`101` / 複数:`101,102,103` / 省略時は直前のIssue |

## パターン判定

| ラベル                   | パターン | docs/コミット |
| ------------------------ | -------- | ------------- |
| `bug`, `critical`        | A（小）  | なし          |
| `feature`, `enhancement` | B（中）  | あり          |

## 手順

### 1. 前提確認

- PR存在確認: `gh pr list --head $(git branch --show-current) --state open`
- DRAFT → Ready: `gh pr ready $PR_NUMBER`
- MERGED → ブランチ削除のみ実行

### 2. docs/コミット（パターンBのみ）

- ステージング: `git add docs/requirements.md docs/specifications.md docs/implementation-guide.md docs/adr/`
- コミット: `git commit -m "docs(docs): update docs for #$ISSUE_NUMBER"`
- プッシュ: `git push origin HEAD`

### 3. PRマージ

PRマージ: `gh pr merge $PR_NUMBER --squash --delete-branch`

### 4. 記録追記

| パターン   | テンプレート                                                      |
| ---------- | ----------------------------------------------------------------- |
| A（小）    | `@.opencode/commands/issue/templates/issue_comment_bug_record.md`               |
| B（中）    | `@.opencode/commands/issue/templates/issue_comment_feature_implementation.md`   |

- テンプレート読込: `$templateContent = Get-Content -Path ".opencode/commands/issue/templates/issue_comment_bug_record.md" -Raw`
- 変数置換: `$templateContent -replace "YYYY-MM-DD", (Get-Date -Format "yyyy-MM-dd") | Out-File -FilePath "temp/comment-body.md" -Encoding utf8`
- コメント追加: `gh issue comment $ISSUE_NUMBER --body-file "temp/comment-body.md"`

### 5. Issueクローズ

Issueクローズ: `gh issue close $ISSUE_NUMBER --reason completed`

### 6. クリーンアップ

- mainに切り替え: `cd <project_root> && git checkout main && git pull`
- worktree削除: `git worktree remove .worktrees/$ISSUE_NUMBER-<type>`
- ブランチ削除: `git branch -d <type>/issue-$ISSUE_NUMBER`
- prune: `git fetch --prune`

### 7. Planファイルのアーカイブ

`.sisyphus/archives` に plan、notepadsを含むファイル一式をアーカイブ

### 8. 完了報告

- マージしたPR番号
- クローズしたIssue番号
- 削除したworktree名・ブランチ名
- docs/コミット内容（パターンBの場合）

## 複数Issueの場合

各Issueに対して手順1〜5を実行後、まとめてクリーンアップ（手順6）を行います。

## エラーハンドリング

| エラー           | 対処                                         |
| ---------------- | -------------------------------------------- |
| PRが存在しない   | エラー終了。「先に /issue-work を実行」      |
| PRがDRAFT        | `gh pr ready` でreadyに変換してからマージ    |
| PRがMERGED済み   | ブランチ削除のみ実行                         |
| コンフリクト     | エラー終了。「手動でコンフリクト解決が必要」 |
| worktree削除失敗 | 強制削除: `git worktree remove --force`      |

> 🎉 フローが完了しました。
