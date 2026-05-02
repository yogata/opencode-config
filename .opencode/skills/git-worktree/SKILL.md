---
name: git-worktree
description: Windows環境でのgit worktree操作を安全に実行するための手順。issue-work/issue-closeコマンドから参照される。
---

# git-worktree

## 動作指針

Windows環境でのgit worktree操作を安全に実行するための手順。issue-work/issue-closeコマンドから参照される。

## 命名規則

- worktreeディレクトリ: `.worktrees/{N}-{type}` （例: `.worktrees/516-fix`, `.worktrees/1-feature`）
- ブランチ名: `{type}/issue-{N}` （例: `fix/issue-516`, `feature/issue-1`）
- `{type}` の取り得る値: `feature`（機能追加・enhancement） / `fix`（バグ修正・bug）
- `{N}` は GitHub Issue 番号（数値）
- この命名規則は `issue-guide` の完了報告フォーマットと一貫性を持つ

## 標準手順

### worktree作成

1. ベースブランチの特定: `main` または `master` を自動検出
2. 作成コマンド: `git worktree add ".worktrees/{N}-{type}" -b "{type}/issue-{N}"`
3. Windows環境: パスにスペースが含まれる可能性があるためダブルクォート必須
4. 作成後の確認: `git worktree list` で正しく追加されたことを検証
5. **絶対パス指定の義務付け**: `workdir` パラメータには絶対パスを使用（相対パスはメインリポジトリの誤編集リスクあり。`docs/tips/inbox.md` の既知バグを反映）

### 既存worktree衝突時の対応

- 同名worktreeが既に存在する場合: `git worktree list` で確認 → 既存worktreeを再利用
- ブランチのみ既存でworktreeがない場合: `git worktree add ".worktrees/{N}-{type}" "{type}/issue-{N}"`（既存ブランチをcheckout）
- ダーティなworktreeの削除は禁止（未コミット変更がある場合はエラー停止、ユーザーに判断を委ねる）

### worktree削除

1. 削除コマンド: `git worktree remove ".worktrees/{N}-{type}"`
2. 削除後のクリーンアップ: `git worktree prune`（手動削除されたディレクトリの残存参照を消去）
3. ブランチ削除: `git branch -d "{type}/issue-{N}"`（マージ済みの場合のみ。未マージなら `-D` は使わずエラー停止）

## 禁止事項

- 相対パスでのworktreeファイル操作禁止（絶対パス必須）
- `--force` によるダーティworktreeの強制削除禁止
- メインリポジトリ（非worktree）内でのファイル編集禁止（issue-work中）
