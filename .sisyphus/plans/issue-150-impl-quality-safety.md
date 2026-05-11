# Issue #150 実装計画: Issue Command実装改善：安全性・品質・状態管理

## 概要
REQ-0010に基づく14項目の実装。Phase 1（事故防止）5件、Phase 2（品質向上）5件、関連タスク4件。

## Work Units

### WU-1: issue-work.md 複合修正
**対象**: `.opencode/commands/issue/issue-work.md`
**要件**: REQ-0010-001, 006, 007, 008
- [ ] REQ-0010-001: line 120 `call_omo_agent(subagent_type="sisyphus", run_in_background=true)` → `task(category="unspecified-high", load_skills=[], run_in_background=true, prompt="...")` に置換
- [ ] REQ-0010-006: Phase Dセクション(115-130)のフォールバック記述(line 130)を冒頭(117行目直後)に移動
- [ ] REQ-0010-007: Guardrails(269-291)の整理
  - ①line 272「要件docの受け入れ基準を尊重しつつ、実装結果を優先する（vibe-coding: 実装先行、REQは事後反映）」を「実装で判明した制約はREQを黙って変更せず、乖離として報告しユーザー承認後に反映する」に置き換える
  - ②line 277 と 287 のverbatim出力重複を統合（277を残して287を削除、または1箇所に統合）
  - ③「vibe-coding（実装先行）の場合も乖離報告を省略しない」旨を明記
- [ ] REQ-0010-008: raw report扱い定義をGuardrailsまたはPhase Dセクションに追加（折りたたみ保持、summary作成可、raw改変禁止）

### WU-2: 単純ファイル修正
**要件**: REQ-0010-003, 004, 005
- [ ] REQ-0010-003: README.md line 109 `.sisyphus/archive/` → `.sisyphus/archives/`, line 128 `archive/` → `archives/`
- [ ] REQ-0010-004: `.gitattributes` をリポジトリルートに新規作成（markdown常にLF、自動CRLF変換防止）
- [ ] REQ-0010-005: issue-backlog.md line 26 `現在日付は2026-05-09` 削除し、実行時システム日付を使用する旨に変更

### WU-3: docs/adr/ 初期化 + draft-meta status
**要件**: REQ-0010-002, 009
- [ ] REQ-0010-002: docs/adr/ を初期化（README.md作成、.gitkeep追加）。README.mdのドキュメント構造ツリーの `docs/adr/` 記載を実在に合わせて修正
- [ ] REQ-0010-009: 以下のファイルの `## draft-meta` セクションに `status` field を追加
  - issue-req.md: `status: draft` をデフォルト値として追加
  - issue-save-req.md: 完了時に `status: saved` に更新するステップを追加
  - issue-create.md: 完了時に `status: issued` に更新するステップを追加
  - issue-close.md: 完了時に `status: closed` に更新するステップを追加

### WU-4: origin/main 明示 + 関連REQ実装タスク
**要件**: REQ-0010-010 + 関連4タスク
- [ ] REQ-0010-010: issue-work.md Step 5のworktree作成 + git-worktree/SKILL.md で `origin/main` を明示
- [ ] issue-save-req.md: `git checkout -- <file>` ロールバックを安全な方法に修正（REQ-0001-007準拠）
- [ ] issue-guide-phases/SKILL.md: SSoT定義を修正（REQ-0001-011準拠。line 40-48のSSoTテーブルの①バイブス壁打ち「Issue本文」はIssue未作成時に矛盾するため修正）
- [ ] issue-req.md: 既存REQ照合ステップ追加（REQ-0002-001準拠。出力に既存REQとの照合結果とUPDATE/CREATE判定を含める）
- [ ] issue-save-req.md: 複数REQ一括UPDATE対応追加（REQ-0002-002準拠）

## 依存関係
- WU-1 と WU-4 は共に issue-work.md を編集 → WU-1が先、WU-4が後（origin/main部分）
- WU-2, WU-3 は独立して並列実行可能
