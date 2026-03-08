---
description: 計画立案からコミットまでを一気通貫で実行する（@plan + /start-work + commit統合）
---

# 実装パイプライン

選択したIssueに対して、計画立案から実装・コミットまでを一気通貫で実行します。常にgit worktreeを使用して、メインの作業ディレクトリを汚さずに開発を行います。

## 引数

| 引数             | 説明                                                  |
| ---------------- | ----------------------------------------------------- |
| `<サブコマンド>` | `test` / `commit` / `pr`（省略時は全ステップ実行）    |
| `<Issue番号>`    | 単一:`101` / 複数:`101,102,103` / 省略時は直前のIssue |

## パターン判定

| ラベル                   | パターン | docs/更新 |
| ------------------------ | -------- | --------- |
| `bug`, `critical`        | A（小）  | なし      |
| `feature`, `enhancement` | B（中）  | あり      |

## 手順

### 1. Issue確認

Issue確認: `gh issue view $ISSUE_NUMBER`

### 2. Worktree作成

- 機能追加: `git worktree add .worktrees/$ISSUE_NUMBER-feature -b feature/issue-$ISSUE_NUMBER`
- バグ修正: `git worktree add .worktrees/$ISSUE_NUMBER-fix -b fix/issue-$ISSUE_NUMBER`
- 作業ブランチ確認: `git branch --show-current`

### 3. worktree作業ルール（重要）

**すべてのファイル操作・コマンド実行はworktree内で行うこと。**

| ツール          | 使用方法                                            |
| --------------- | --------------------------------------------------- |
| read/write/edit | パスは `.worktrees/$ISSUE_NUMBER-<type>/...` を指定 |
| bash            | `workdir=".worktrees/$ISSUE_NUMBER-<type>"` を使用  |
| glob/grep       | `path=".worktrees/$ISSUE_NUMBER-<type>"` を使用     |

**【禁止事項】**

- メインディレクトリ（`C:\Users\...\fujoho_schedule`）直下でのファイル作成・編集
- `cd .worktrees/... && command` パターン（セッションが独立のため無効）

### 4. 計画立案

`@plan Issue #$ISSUE_NUMBERの実装計画を立ててください。実装計画のファイル名はissue番号と関連付けてください。`

### 5. 実装開始

`/start-work`

### 6. docs/更新（パターンBのみ）

- HLD更新: `docs/specifications.md` — テンプレート: `@.opencode/commands/issue/templates/doc_hld.md`
- LLD作成: `docs/implementation-guide.md` — テンプレート: `@.opencode/commands/issue/templates/doc_lld.md`
- ADR更新: `docs/adr/NNN-xxx.md` の status を `proposed` → `accepted`

### 7. テスト

`/verification-before-completion`

### 8. コミット

- 変更ファイル確認: `git diff --name-only HEAD`
- ステージング: `git add -A`
- 機能追加: `git commit -m "feat: <実装内容の要約> (#$ISSUE_NUMBER)"`
- バグ修正: `git commit -m "fix: <修正内容の要約> (#$ISSUE_NUMBER)"`
- プッシュ: `git push origin HEAD`

**注意**: docs/のコミットは `/issue-close` で行う

### 9. PR作成

**テンプレート**: `@.opencode/commands/issue/templates/pr_desc.md`

- temp/作成: `New-Item -ItemType Directory -Path "temp" -Force`（存在しない場合）
- テンプレート読込: `$templateContent = Get-Content -Path ".opencode/commands/issue/templates/pr_desc.md" -Raw`
- 変数置換: `$templateContent -replace "#\{ISSUE_NUMBER\}", $ISSUE_NUMBER | Out-File -FilePath "temp/pr-body.md" -Encoding utf8`
- PR作成: `gh pr create --base main --title "feat/fix: {要約} (#$ISSUE_NUMBER)" --body-file "temp/pr-body.md"`

### 10. レビュー依頼

> PRを作成/更新しました: {PR_URL}
> ユーザーレビューをお願いします.
>
> - OK → `/issue-close`
> - NG（仕様バグ）→ `/issue-update` → `/issue-work`
> - NG（実装バグ）→ `/issue-update --comment` → `/issue-work`

## 複数Issueの場合

1. 各Issueの影響ファイルを予測
2. 競合なし → 並列実行 / 競合あり → 直列実行
3. 各worktreeで手順1-9を実行

## エラーハンドリング

| エラー               | 対処                             |
| -------------------- | -------------------------------- |
| worktree作成失敗     | クリーンアップして終了           |
| 並列タスクの一部失敗 | 成功分は維持、失敗分を報告       |
| 同一ブランチ名の競合 | 既存ブランチを削除またはスキップ |
| PR作成失敗           | コミットは保持、手動PR作成を案内 |

## 中断時の対応

`/branch-cleanup`

## 次のステップ

> ✅ 完了しました。次のステップ: `/issue-close`
