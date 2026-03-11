---
description: 計画立案からコミットまでを一気通貫で実行する（@plan + /start-work + commit統合）
---

# 実装パイプライン

選択したIssueに対して、計画立案から実装・コミットまでを一気通貫で実行します。常にgit worktreeを使用して、メインの作業ディレクトリを汚さずに開発を行います。

---

## 入力（SSoT）

- **GitHub Issue** — open状態

## 出力（SSoT）

- **GitHub PR** — open状態（レビュー待ち）
- **worktree + ブランチ** — `.worktrees/$ISSUE_NUMBER-<type>`

## 完了後のフェーズ

`review` — PR作成済み・レビュー中

---

## 前提

`@issue-workflow` スキルを実行し、以下を取得してください：

- パターン（A/B）判定
- Issue情報の確認

## 引数

- `<サブコマンド>` — `test` / `commit` / `pr`（省略時は全ステップ実行）
- `<Issue番号>` — 単一:`101` / 複数:`101,102,103` / 省略時は[省略可能条件]を参照

**【省略可能条件】**

Issue番号省略が可能なのは以下の場合のみ：

- 同一セッション内で `/issue-create` により Issue が作成されている
- かつ、その Issue 番号がセッションコンテキストに保持されている

**【禁止事項】**

以下の推測方法は明示的に禁止：

- `.worktrees` ディレクトリからの Issue 番号推測
- `git branch` からの Issue 番号推測
- `gh issue list` からの最新 Issue 取得
- その他、セッションコンテキスト以外からの推測

省略時かつ条件を満たさない場合、`ISSUE_NUMBER_REQUIRED` エラーで停止する。

## 手順

### フェーズ1: 準備

#### 1. Issue確認

Issue確認: `gh issue view $ISSUE_NUMBER`

#### 2. Worktree作成

- 機能追加: `git worktree add .worktrees/$ISSUE_NUMBER-feature -b feature/issue-$ISSUE_NUMBER`
- バグ修正: `git worktree add .worktrees/$ISSUE_NUMBER-fix -b fix/issue-$ISSUE_NUMBER`
- 作業ブランチ確認: `git branch --show-current`

#### 3. worktree作業ルール（重要）

**すべてのファイル操作・コマンド実行はworktree内で行うこと。**

- **read/write/edit** — パスは `.worktrees/$ISSUE_NUMBER-<type>/...` を指定
- **bash** — `workdir=".worktrees/$ISSUE_NUMBER-<type>"` を使用
- **glob/grep** — `path=".worktrees/$ISSUE_NUMBER-<type>"` を使用

**【禁止事項】**

- メインディレクトリ直下でのファイル作成・編集
- `cd .worktrees/... && command` パターン（セッションが独立のため無効）

### フェーズ2: 実装

#### 4. 計画立案

`@plan Issue #$ISSUE_NUMBERの実装計画を立ててください。テストケースを含めてください。実装計画のファイル名はissue番号と関連付けてください。`

#### 5. TDD実装

`/start-work`（RED: テスト作成 → GREEN: 実装 → REFACTOR: 整理）

**開発中（ウォッチモード）**:

- 型チェック・テストは各プロジェクトの構成に合わせて実行

### フェーズ3: ドキュメント更新（パターンBのみ）

#### 6. docs/更新

- HLD更新: `docs/specifications.md`
  — テンプレート: `@.opencode/commands/issue/templates/doc_hld.md`
- LLD作成: `docs/implementation-guide.md`
  — テンプレート: `@.opencode/commands/issue/templates/doc_lld.md`
- ADR更新: `docs/adr/NNN-xxx.md` の status を `proposed` → `accepted`

### フェーズ4: 検証・完了

#### 7. ローカル検証（必須）

- 型チェック: `npx tsc --noEmit`
- Lint: `bun run lint`
- ビルド: `bun run build`
- ユニット: `bun test`
- E2E: `bun run test:e2e`

**失敗時**: 修正して手順7を再実行（手順8へ進まない）

#### 8. コミット

- 変更ファイル確認: `git diff --name-only HEAD`
- ステージング: `git add -A`
- 機能追加: `git commit -m "feat: <実装内容の要約> (#$ISSUE_NUMBER)"`
- バグ修正: `git commit -m "fix: <修正内容の要約> (#$ISSUE_NUMBER)"`
- プッシュ: `git push origin HEAD`

**注意**: docs/のコミットは `/issue-close` で行う

#### 9. PR作成

**テンプレート**: `@.opencode/commands/issue/templates/pr_desc.md`

- テンプレートからPR本文を生成し、`temp/pr-body.md` に保存
- PR作成: `gh pr create --base main --title "feat/fix: {要約} (#$ISSUE_NUMBER)" --body-file "temp/pr-body.md"`

#### 10. デプロイ検証（必須）

PR作成後、デプロイプラットフォームでのビルドが正常に完了したことを確認します。

- CI/CDパイプラインのステータス確認: `gh pr checks $PR_NUMBER`
- デプロイプラットフォームのダッシュボードでビルドログを確認
- ビルドエラーがないことを検証

**失敗時**: エラー原因を特定し、修正後に手順8（コミット）から再実行

**注意**: プレビュー環境へのデプロイが完了するまで待機し、実際の動作確認が可能な状態であることを確認

## 複数Issueの場合

1. 各Issueの影響ファイルを予測
2. 競合なし → 並列実行 / 競合あり → 直列実行
3. 各worktreeで手順1-10を実行

## 中断時の対応

`/branch-cleanup`

## 完了時

`@issue-workflow` スキルの「完了報告生成」と「次のステップ提案」を実行してください。

現在のコンテキスト:

- コマンド: issue-work
- Issue番号: $ISSUE_NUMBER
- PR番号: $PR_NUMBER
- PR URL: $PR_URL
- パターン: $PATTERN

## エラーハンドリング

`@issue-workflow` スキルのエラーハンドリングを参照してください。
