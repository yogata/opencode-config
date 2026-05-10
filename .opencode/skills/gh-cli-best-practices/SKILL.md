---
name: gh-cli-best-practices
description: Enforces safe GitHub CLI usage on Windows (PowerShell/cmd) by routing all multi-line body content through temporary files to prevent encoding and escaping errors. USE FOR: running gh issue/pr/release commands with --body or --body-file, reading gh command output safely on Windows, or writing Issue/PR content. DO NOT USE FOR: basic gh commands without body content, non-Windows environments, or general git operations.
---

# gh-cli-best-practices

## 動作指針

Windows上の標準シェル（PowerShell/cmd.exe）の制約を回避するため、以下の手順を強制する。

## WRITE操作（書き込み安全性）

### 1. 禁止事項

- `gh` コマンドの引数として `--body "..."` を直接使用することを禁止する。
- `<<EOF` (HEREDOC) 構文によるファイル作成を禁止する。

### 2. 標準手順

1. テキスト（Issue本文、PR説明など）を一時ファイル `.sisyphus/tmp/gh-temp-{timestamp}.md` に書き出す。
2. 保存形式は **UTF-8 (BOMなし)**、改行コードは **LF** とする。
3. `gh` コマンド実行時に `--body-file` または `--notes-file` オプションで該当ファイルを指定する。
4. 実行完了後、一時ファイルを削除する。

## READ操作（読み取り安全性）

### 3. 安全な読み取り手順

1. `gh` コマンドの出力を一時ファイル `.sisyphus/tmp/gh-read-{timestamp}.md` にリダイレクトする（PowerShell: `gh ... > .sisyphus/tmp/gh-read-{timestamp}.md` または `gh ... | Out-File -FilePath .sisyphus/tmp/gh-read-{timestamp}.md -Encoding utf8`）。
2. Read tool で一時ファイルを読み取る。
3. 読み取り完了後、一時ファイルを削除する。
4. 保存形式は **UTF-8 (BOMなし)**、改行コード **LF** とする。

### 4. 読み取り禁止事項

- `gh` コマンドの出力をPowerShell変数に直接格納することを禁止（`$var = gh ...`）。
- `gh` コマンドの出力をサブ式で直接使用することを禁止（`$(gh ...)`）。
- **理由**: Windows PowerShellはUTF-8出力をShift-JISとして解釈し、日本語が文字化けするため。
