---
name: nextjs-supabase-error-patterns
description: Next.js 16、Supabase、TypeScript で構築されたアプリケーションで実際に発生したエラーとその対処法。特殊なエラーや特殊な対応を除外し、再利用可能な学びを抽出している。エラーが発生した際の対処法を探したい場合、コードレビュー時に同様の問題が起きるのを防ぎたい場合、プロジェクト固有の技術的制約やパターンを確認したい場合、新しい機能を実装する際に過去の失敗を繰り返さないようにしたい場合に使う。
---

# nextjs-supabase-error-patterns

## エラーパターン一覧

このSKILLは以下のカテゴリーに分類されたエラーパターンと対処法を提供します。

### Next.js 16 関連のエラー

→ 詳細は [nextjs16-errors.md](references/nextjs16-errors.md) を参照

- cookies() が async 関数であることへの対応
- useSearchParams を使用するページの Suspense boundary

### TypeScript のスコープ問題

→ 詳細は [typescript-scope.md](references/typescript-scope.md) を参照

- try-catch ブロックのスコープ問題

### データベース・マイグレーション関連

→ 詳細は [database-migrations.md](references/database-migrations.md) を参照

- Migration ファイル名の番号衝突
- upsert の onConflict 設定ミス
- スキーマ変更後のカラム参照エラー

### 認証・認可関連

→ 詳細は [auth-authorization.md](references/auth-authorization.md) を参照

- WWW-Authenticate ヘッダーの問題
- Basic認証のセキュリティ問題
- ハードコーディングされた認証情報の削除

### スクレイパー・データ処理関連

→ 詳細は [scraper-data-processing.md](references/scraper-data-processing.md) を参照

- 日付計算のロジックミス
- 正規表現の桁数制限
- 重複したコードの削除

### 型定義とコンポーネント設計

→ 詳細は [type-definitions.md](references/type-definitions.md) を参照

- any型の乱用
- Props型の過剰な指定

### UI/UX 改善

→ 詳細は [ui-ux.md](references/ui-ux.md) を参照

- テーブルの列順序と幅調整
- スクロール表示の改善

### デバッグと本番環境

→ 詳細は [debugging-production.md](references/debugging-production.md) を参照

- デバッグログの制御

### マイグレーション構造の改善

→ 詳細は [migration-structure.md](references/migration-structure.md) を参照

- マイグレーションディレクトリ構造

### ミドルウェアの実装

→ 詳細は [middleware.md](references/middleware.md) を参照

- パス保護の実装
- Next.js 16 のプロキコンベンションへの移行

### CI/CD 関連

→ 詳細は [github-actions-dependencies.md](references/github-actions-dependencies.md) を参照

- MODULE_NOT_FOUND error for dotenv
- 依存関係のインストール効率化

## 使用方法

エラーが発生した際は、カテゴリー（Next.js 16、TypeScript、データベース、認証など）を特定し、対応する reference ファイルを参照して対処法を適用してください。各パターンの「学び」は今後の開発に活用してください。

## 関連するSKILL

- **vercel-react-best-practices**: ReactとNext.jsのパフォーマンス最適化
- **systematic-debugging**: バグ修正の体系的なアプローチ
- **verification-before-completion**: 修正後の検証フロー

## 参考情報

- Next.js 16 ドキュメント: https://nextjs.org/docs
- Supabase ドキュメント: https://supabase.com/docs
- TypeScript ドキュメント: https://www.typescriptlang.org/docs/
