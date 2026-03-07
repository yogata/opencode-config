---
name: gh-cli-best-practices
description: Windows環境(cmd/PS)で GitHub CLI (gh issue/pr/release) を使用する際、複数行の本文(--body)によるエスケープエラーを回避し、一時ファイル(--body-file)経由の実行を強制する。
---

# gh-cli-best-practices

## 動作指針

Windows上の標準シェル（PowerShell/cmd.exe）の制約を回避するため、以下の手順を強制する。

### 1. 禁止事項

- `gh` コマンドの引数として `--body "..."` を直接使用することを禁止する。
- `<<EOF` (HEREDOC) 構文によるファイル作成を禁止する。

### 2. 標準手順

1. テキスト（Issue本文、PR説明など）を一時ファイル `.sisyphus/tmp/gh-temp-{timestamp}.md` に書き出す。
2. 保存形式は **UTF-8 (BOMなし)**、改行コードは **LF** とする。
3. `gh` コマンド実行時に `--body-file` または `--notes-file` オプションで該当ファイルを指定する。
4. 実行完了後、一時ファイルを削除する。
