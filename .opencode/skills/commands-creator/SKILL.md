---
name: commands-creator
description: OpenCodeでカスタムコマンドを作成、設定、使用する場合、繰り返し発生するタスクを自動化したい時
---

# commands-creator

## Overview
カスタムコマンドは、OpenCode TUIで繰り返し使用するタスクを自動化するためのプロンプトを定義する仕組みです。コマンド名を入力するだけで、定義済みのプロンプトがLLMに送信され、一貫性のある作業が可能になります。

## When to Use

**カスタムコマンドを作成すべき状況：**
- 同じ種類のタスクを何度も実行している（コードレビュー、テスト実行、コンポーネント作成など）
- 複雑なプロンプトを毎回手入力するのが面倒
- チーム内で一貫した作業フローを共有したい
- 特定のエージェントやモデルでタスクを実行したい

**使用すべきでない状況：**
- 一回限りの特別なタスク
- 既存の組み込みコマンド（`/init`, `/undo`, `/redo`, `/share`, `/help`）で十分

## Core Pattern

コマンドは2つの方法で定義できます：

### 方法1: JSON Config

`opencode.json` で定義（プロジェクト単位）：

```json
{
  "command": {
    "test": {
      "template": "Run full test suite with coverage report.\nFocus on failing tests and suggest fixes.",
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}
```

### 方法2: Markdown Files

`~/.config/opencode/commands/` または `.opencode/commands/` に `.md` ファイルを作成：

```markdown
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---
Run full test suite with coverage report and show any failures.
Focus on failing tests and suggest fixes.
```

ファイル名（例：`test.md`）がコマンド名（`/test`）になります。

## Quick Reference

| 設定項目 | 説明 | 必須/任意 |
|----------|------|-----------|
| `template` | LLMに送信するプロンプト（Markdownファイルの場合は本文） | 必須 |
| `description` | TUIで表示される説明 | 任意 |
| `agent` | 実行するエージェント名 | 任意 |
| `subtask` | サブエージェントとして実行するか（true/false） | 任意 |
| `model` | 使用するモデル名 | 任意 |

| プレースホルダー | 説明 | 例 |
|--------------|------|-----|
| `$ARGUMENTS` | すべての引数 | `/component Button` → `Button` |
| `$1`, `$2`, `$3` | 位置パラメータ（1番目、2番目...） | `/create-file config.json src` → `$1=config.json`, `$2=src` |
| `!\`command\`` | シェルコマンド出力の注入 | `!\`npm test\`` |
| `@filename` | ファイル参照 | `@src/components/Button.tsx` |

## Implementation

### ステップ1: 保存場所を選択

- **グローバルコマンド**: `~/.config/opencode/commands/`
- **プロジェクトコマンド**: `.opencode/commands/`

### ステップ2: ファイル作成

Markdown形式でコマンドファイルを作成：

**シンプルな例（引数なし）**:

```markdown
---
description: テストを実行してカバレッジを確認
---
テストスイート全体を実行し、カバレッジレポートを表示してください。
失敗したテストに焦点を当て、修正案を提案してください。
```

**引数付きの例**:

```markdown
---
description: Reactコンポーネントを作成
---
$ARGUMENTS という名前の新しいReactコンポーネントを作成してください。
TypeScriptサポートを含め、適切な型定義と基本的な構造を持つようにしてください。
```

実行: `/component Button`

**位置パラメータを使用する例**:

```markdown
---
description: ファイルを指定ディレクトリに作成
---
$1 という名前のファイルを $2 ディレクトリに作成し、以下の内容を含めてください：$3
```

実行: `/create-file config.json src "{ \"key\": \"value\" }"`

### ステップ3: シェルコマンドを組み込む

コマンド実行時にシェルコマンドの結果をプロンプトに含められます：

```markdown
---
description: テストカバレッジを分析
---

現在のテスト結果：!\`npm test\`

これらの結果に基づいて、カバレッジを向上させるための改善案を提示してください。
```

### ステップ4: ファイルを参照する

特定のファイルの内容を自動的に含められます：

```markdown
---
description: コンポーネントをレビュー
---

@src/components/Button.tsx のコンポーネントをレビューしてください。
パフォーマンスの問題がないか確認し、改善案を提示してください。
```

### ステップ5: エージェントとモデルを指定

特定のエージェントやモデルで実行させたい場合：

```markdown
---
description: アーキテクチャを計画
agent: plan
model: anthropic/claude-3-5-sonnet-20241022
---
現在のプロジェクトのアーキテクチャを分析し、改善点を提案してください。
```

### ステップ6: サブタスクとして実行

メインコンテキストを汚染したくない場合：

```markdown
---
description: 深い分析を実行
subtask: true
---
この問題の根本原因を徹底的に調査してください。
```

## Common Mistakes

| 間違い | 影響 | 修正 |
|----------|--------|------|
| YAMLフロントマターを省略 | コマンドが認識されない | `---` で囲んだYAMLセクションを必ず含める |
| ファイル名にスペースを使用 | コマンド実行が困難 | ハイフン（`-`）を使用：`my-command.md` |
| 引数のエスケープ忘れ | 引数が正しく渡らない | クォートを使用: `/create-file data.json src "{ \"key\": \"value\" }"` |
| 相対パスで `@` ファイル参照 | ファイルが見つからない | プロジェクトルートからの絶対パスまたは正しい相対パスを使用 |
| シェルコマンドで失敗を無視 | プロンプトに不完全な情報が含まれる | `!\`npm run build\`` が成功することを確認して使用 |
| 組み込みコマンドをオーバーライド | 便利な組み込み機能が使えなくなる | `/init`, `/help` などをオーバーライドしないように注意 |

## Real-World Impact

- **効率化**: チーム全体で「コードレビューチェックリスト」を共有することで、レビュー品質が均質化
- **一貫性**: 毎回同じプロンプトを入力するミスを排除、レビュー基準が統一
- **自動化**: `npm test && coverage` のような定型的なコマンド実行と結果分析をワンショットで完結
