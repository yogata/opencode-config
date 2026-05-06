---
id: REQ-0005
title: "issue-reqワークフローの再設計（承認ゲート・パターン分離・命名統一）"
classification: FR
category: maintainability
status: planned
adr: null
created: "2026-05-06"
updated: "2026-05-06"
tags: [workflow, req, phase-boundary, approval-gate]
related_to: [REQ-0003]
depends_on: []
---

> このドキュメントは①バイブス壁打ちフェーズで作成されます。

# issue-reqワークフローの再設計（承認ゲート・パターン分離・命名統一）

## 背景・課題

現行のissue-reqワークフローに以下の問題がある：

1. **commit/pushのタイミングが壁打ちコマンドに埋め込まれている**: issue-reqがgit操作を含んでおり、壁打ちの再実行や途中修正が困難
2. **REQファイルにIssue番号フィールドが存在**: issue-req時点ではIssue番号が未割り当てのため、フィールドが空のままcommitされる。誰も埋めない盲点があった
3. **パターンA（バグ修正）でもREQファイルが作成される**: 軽微なバグ修正にREQファイルは過剰。REQ修正が必要ならパターンBに昇格すべき
4. **REQファイルパス命名に揺れ**: `REQ-NNNN` と `REQ-NNNN-slug` が混在

## 目標

1. issue-reqの最終ステップにユーザー承認ゲートを追加し、承認後にcommit & pushする
2. REQファイルのfrontmatterから`issue`フィールドを削除する（Issue→REQの一方向参照）
3. パターンA（バグ修正・軽微変更）ではREQファイルを作成しない。REQファイル修正が必要な場合はパターンBに昇格する
4. REQファイルパス命名を`REQ-NNNN-slug`に統一する

## 方向性

最小限の変更で4つの改善を適用する。既存の3フェーズ体系（①壁打ち→②実行→③完了）は維持し、承認ゲートは①壁打ちフェーズの完了アクションとして位置づける。

## 機能要件

- [ ] issue-reqに承認ゲートを追加
  - **Given**: issue-reqのSteps 1-6（壁打ち→REQ作成→整合性検証）が完了している
  - **When**: REQ内容をユーザーに提示し、承認を求める
  - **Then**: ユーザーが承認した場合のみdocs変更をcommit & pushする。差し戻しの場合は壁打ちを継続する
  - **補足**: 承認ゲートはissue-reqのStep 7として定義する。既存のcommit & pushはStep 8に繰り下げ

- [ ] REQファイルの`issue`フィールドを削除
  - **Given**: REQファイルのfrontmatterに`issue`フィールドが存在する
  - **When**: doc_requirement.mdテンプレートおよびreq-file-managerスキルから`issue`フィールドの定義を削除する
  - **Then**: REQファイルのfrontmatterに`issue`フィールドが含まれなくなる。既存REQファイル（REQ-0001~0004）からも`issue`フィールドを削除する

- [ ] パターンAでのREQファイル作成をスキップ
  - **Given**: issue-reqでパターンA（バグ修正・軽微変更）と判定された
  - **When**: issue-reqのREQファイル作成ステップに到達する
  - **Then**: REQファイルを作成せず、コミットも行わない。Issue本文のみで要件を管理する
  - **補足**: REQファイルの修正が必要なバグ修正は、パターンB（機能追加）に昇格して扱う

- [ ] REQファイルパス命名を`REQ-NNNN-slug`に統一
  - **Given**: REQファイルパスに`REQ-NNNN`と`REQ-NNNN-slug`の2形式が混在している
  - **When**: 全コマンド・スキル・テンプレートのREQファイルパス参照を`REQ-NNNN-slug`に統一する
  - **Then**: `docs/requirements/`内の全REQファイルが`REQ-NNNN-slug.md`形式になる

**品質基準**: 各要件は 測定可能・一意・実装可能 であること。

## 非機能要件

- 既存の3フェーズ体系を維持する（新フェーズの追加なし）
- 既存のissue-*ワークフローに破壊的変更を加えない（ステップ・フィールドの追加・削除のみ）
- REQ-0003（docsコミット義務化）は本REQに置き換えるためdeprecatedに更新する

## スコープ

### 対象

- `.opencode/commands/issue/issue-req.md`（承認ゲート追加、パターンA/B分岐明記）
- `.opencode/skills/issue-guide/SKILL.md`（完了報告フォーマット更新、フェーズ境界ルール更新、コマンド詳細更新）
- `.opencode/skills/req-file-manager/SKILL.md`（`issue`フィールド削除、パス命名`NNNN-slug`統一、パターンA除外記述）
- `.opencode/commands/issue/templates/doc_requirement.md`（`issue`フィールド削除、パス命名更新）
- `.opencode/commands/issue/issue-create.md`（REQパス命名`NNNN-slug`反映、パターンA/B対応）
- `.opencode/commands/issue/issue-work.md`（REQファイル前提の記述確認・調整）
- `.opencode/commands/issue/issue-close.md`（docs検証のREQ参照パス更新）
- `.opencode/commands/issue/issue-update.md`（`--req`時のREQパス命名更新）
- `docs/requirements/REQ-0001.md` ~ `REQ-0004.md`（`issue`フィールド削除、ファイル名`NNNN-slug`へリネーム）
- `docs/requirements/README.md`（インデックスのパス更新）

### 対象外

- `REQ-0004`の内容（agent変更と壁打ち結論ハイライト）— 独立した要件として別途対応
- コマンドのagent指定の変更
- テンプレートの構造変更（フィールド削除・パス命名修正のみ）
- `docs/specs/` の更新（issue-closeで実施）

## 関連情報

- **置き換え**: REQ-0003（①→②フェーズ境界でのdocsコミット義務化）— 本REQに包括的に置き換え
- **関連**: REQ-0004（issue-*コマンドのagent変更と壁打ち結論ハイライト追加）— 独立要件
