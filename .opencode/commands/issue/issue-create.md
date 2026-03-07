---
description: /issue-req の結果をもとにGitHub Issueを作成する
---

# Issue登録

/issue-req で定義した要件または分析結果をもとに、GitHub Issueを作成します。

## パターン判定

| パターン    | 処理                               | ラベル                   |
| ----------- | ---------------------------------- | ------------------------ |
| **A（小）** | Issue本文 + コメント（分析結果）   | `bug` 等                 |
| **B（中）** | Issue本文 + コメント + docs/紐付け | `feature`, `enhancement` |

## テンプレート

| パターン    | Issue本文                                              | コメント                                                   |
| ----------- | ------------------------------------------------------ | ---------------------------------------------------------- |
| **A**       | `@.opencode/commands/issue/templates/issue_desc_bug.md`     | `@.opencode/commands/issue/templates/issue_comment_bug_analysis.md`      |
| **B**       | `@.opencode/commands/issue/templates/issue_desc_feature.md` | `@.opencode/commands/issue/templates/issue_comment_feature_technical.md` |

## 手順

### 共通

1. **temp/ディレクトリ作成**: `New-Item -ItemType Directory -Path "temp" -Force`（存在しない場合）
2. **Issue本文作成**: テンプレートから作成し、`temp/issue-body.md` に保存
3. **Issue作成**: `gh issue create --title "<タイトル>" --body-file "temp/issue-body.md" --label "<ラベル>"`
4. **コメント追加**: テンプレートから作成し、`temp/comment-body.md` に保存後、`gh issue comment $ISSUE_NUMBER --body-file "temp/comment-body.md"`

### パターンBのみ

5. **docs/紐付け**: `docs/requirements.md` と `docs/adr/NNN-xxx.md` にIssue番号を追記

## 完了報告

- パターンA: `✅ Issue #{N} を作成しました（パターンA）。次のステップ: /issue-work {N}`
- パターンB: `✅ Issue #{N} を作成しました（パターンB）。docs/にIssue番号を紐付けました。次のステップ: /issue-work {N}`

## ラベル選定

| パターン      | ラベル                   |
| ------------- | ------------------------ |
| A（バグ修正） | `bug`, `critical`        |
| B（機能追加） | `enhancement`, `feature` |

既存ラベル確認: `gh label list`
