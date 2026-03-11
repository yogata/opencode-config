---
description: /issue-req の結果をもとにGitHub Issueを作成する
---

# Issue登録

`@issue-workflow` スキルで判定したパターンに基づき、GitHub Issueを作成します。

## 前提

`@issue-workflow` スキルを実行し、以下を取得してください：

- パターン（A/B）判定
- ラベル選定

## 手順

### 共通

1. **temp/ディレクトリ作成** — 存在しない場合のみ作成
2. **Issue本文作成**: 判定したパターンのテンプレートから作成し、`temp/issue-body.md` に保存
   - パターンA: `@.opencode/commands/issue/templates/issue_desc_bug.md`
   - パターンB: `@.opencode/commands/issue/templates/issue_desc_feature.md`
3. **Issue作成**: `gh issue create --title "<タイトル>" --body-file "temp/issue-body.md" --label "<ラベル>"`
4. **コメント追加**: 判定したパターンのテンプレートから作成し、`temp/comment-body.md` に保存後、`gh issue comment $ISSUE_NUMBER --body-file "temp/comment-body.md"`
   - パターンA: `@.opencode/commands/issue/templates/issue_comment_bug_analysis.md`
   - パターンB: `@.opencode/commands/issue/templates/issue_comment_feature_technical.md`

### パターンBのみ

5. **docs/紐付け**: `docs/requirements.md` と `docs/adr/NNN-xxx.md` にIssue番号を追記

## 完了時

`@issue-workflow` スキルの「完了報告生成」と「次のステップ提案」を実行してください。

現在のコンテキスト:

- コマンド: issue-create
- Issue番号: {N}
- パターン: {判定結果}

## エラーハンドリング

`@issue-workflow` スキルのエラーハンドリングを参照してください。
