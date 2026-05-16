# System Specification

## コマンドシステム

### issue-*コマンド群

issue-*コマンドは9つのコマンドで構成され、3マクロフェーズの開発ワークフローを提供する。

| コマンド | 役割 | エージェント |
|---|---|---|
| `/issue/issue-req` | 要件定義（壁打ち）。単体実行時にStep 0でセッションコンテキスト検知を行い、既存要件情報を推論・引き継ぎして適切なStepへルーティングする | prometheus |
| `/issue/issue-save-req` | 要件定義の保存 | sisyphus |
| `/issue/issue-create` | Issue登録（Epic + 子Issue一括作成対応） | sisyphus |
| `/issue/issue-work` | 実装パイプライン（3フェーズ構成: 準備→実装→提出）。Step 11にデプロイ検証（`gh pr checks`）を含む。複数Issueの並列実行に対応 | sisyphus |
| `/issue/issue-update` | Issue更新 | sisyphus |
| `/issue/issue-close` | 完了処理 | sisyphus |
| `/issue/issue-next` | 次コマンド推論 | sisyphus |
| `/issue/issue-backlog` | バックログ抽出（残課題の抽出・分類・解消チェック → draft保存）。ショートカット経路 | sisyphus |
| `/issue/issue-backlog-create` | バックログIssue作成（承認済みdraftからEpic + 子Issue作成・マークコメント投稿） | sisyphus |

コマンドごとに適切なagentをfrontmatterに指定（issue-req: prometheus、それ以外: sisyphus）。

### tips-*コマンド群

tips-*コマンドは3つのコマンドで構成され、学びの3層パイプライン（キャプチャ→分析→昇華）を提供する。

| コマンド | 役割 | 層 |
|---|---|---|
| `/tips/tips-add` | inbox.mdに学びを追加 | キャプチャ層 |
| `/tips/tips-refactor` | セマンティック分析→evaluation-report.md→archive移動 | 分析層 |
| `/tips/tips-elevate` | 昇華判定→stagingスタブ生成 | 昇華層 |

データフロー: `inbox.md` →（tips-refactor）→ `archive.md` + `evaluation-report.md` →（tips-elevate）→ `elevation-staging/`

補助スキル: `tips-capture`（学び検知・追加提案、issue-closeに統合）

### 品質メトリクス

`deviation-check`: 乖離検出時に品質メトリクスを自動収集する。メトリクス定義は `docs/specs/quality-specs.md` で管理。型チェック・Lint・ビルド・テスト結果を収集し、乖離検出報告に併記してPR本文に反映する。

### 安全性スキル

`gh-cli-best-practices`: Windows PowerShell環境でのgh CLI使用時の安全性を確保する。WRITE操作（`--body-file`経由）、READ操作（一時ファイル経由でRead tool使用）、VERIFY操作（書き込み後の読み戻し検証）の3つをカバーし、文字化け防止と内容品質を担保する。VERIFY操作は3観点（エンコーディング・Markdown構造・テンプレート必須セクション）で検証し、最大3回の自動リトライを行う。issue-create、issue-work、issue-closeの各書き込み操作後に適用する。

### Epic（大規模Issue分割フロー）

規模判定条件（3条件のいずれか1つ）を満たす場合、`scale: large`（Epic）として扱う:
1. 複数モジュール跨ぎ（UI + API + DB等）
2. 単一PRでのPR肥大化リスク
3. 段階的リリースが必要

**ワークフロー**: `issue-req`（規模判定）→ `issue-save-req` → `issue-create`（Epic + 子Issue一括作成）→ `issue-work`（子Issue並列実行）→ 各 `issue-close` → Epic自動クローズ

**データフロー**: 1ドラフト → 1 REQ → Epic + N子Issue（REQ分割なし）

### Issueテンプレートの完了条件セクション

Issueテンプレート（`issue_desc_*.md`）に`完了条件`セクションを【必須】項目として配置する。位置は`受け入れ条件`セクションの直前とする。

**適用テンプレート**: `issue_desc_feature.md`, `issue_desc_bug.md`, `issue_desc_child.md`, `issue_desc_backlog_child.md`

**Epicテンプレートの扱い**: `issue_desc_epic.md`, `issue_desc_backlog_epic.md`には`完了条件`セクションを追加せず、既存の`受け入れ条件`にEpic全体の完了判定条件としての明確化コメントを付与する。

**コマンドへの反映**: `issue-create`のguardrailで`完了条件`を必須セクションとして確認する。`issue-work`のStep 7で完了条件品質ゲートを実施し、完了判定はPlanではなく`完了条件`を参照する。

### Epic自動クローズ

`issue-close` Step 8 で親Epic本文更新後、Epic内の全子Issue状態を確認し、全完了時にEpicを自動クローズする。子Issue残存時はスキップし完了報告に状況を表示する。

### Epicステータス追跡

`epic-status-tracker`: 親Epic Issueのステータス追跡テーブル（`| # | Issue | ステータス | 内容 |` 4列形式）を更新する知識ベース。ステータス値は `☐ 未着手` / `🔄 進行中` / `✅ 完了 ([PR#N](URL))` / `❌ 対処不要` の4値。`issue-work` Phase A で `🔄 進行中` に更新し、`issue-close` Step 8 で `✅ 完了 ([PR#N](URL))` に更新する。`❌ 対処不要` は手動設定のみの終了状態。Epic自動クローズ判定では `❌ 対処不要` を `✅ 完了` と同等の終了状態として扱う。多重Issueモードでは親エージェントがWave開始前に一括更新する。
