# Conventional Commits タイプリファレンス / Conventional Commits Types Reference

## 目次 / Table of Contents

1. [feat](#feat) - 新機能 / New Feature
2. [fix](#fix) - バグ修正 / Bug Fix
3. [docs](#docs) - ドキュメント / Documentation
4. [style](#style) - スタイル / Code Style
5. [refactor](#refactor) - リファクタリング / Refactoring
6. [perf](#perf) - パフォーマンス改善 / Performance Improvement
7. [test](#test) - テスト / Tests
8. [build](#build) - ビルドシステム / Build System
9. [ci](#ci) - CI/CD設定 / CI/CD Configuration
10. [chore](#chore) - その他の変更 / Other Changes
11. [revert](#revert) - 取り消し / Revert
12. [Breaking Changes](#breaking-changes) - 破壊的変更 / Breaking Changes

---

## feat

### 説明 / Description
新しい機能を追加するコミットタイプです。ユーザーに新しい価値を提供する変更を表します。

### SemVer レベル
**MINOR**

### 使用例 / Examples

**英語:**
```
feat: add user authentication system
feat(api): implement user registration endpoint
feat(ui): create responsive navigation component
feat(database): add user preferences table
feat(auth): support OAuth2 integration
```

**日本語:**
```
feat: ユーザー認証システムを追加
feat(api): ユーザー登録エンドポイントを実装
feat(ui): レスポンシブなナビゲーションコンポーネントを作成
feat(database): ユーザー設定用のテーブルを追加
feat(auth): OAuth2連携に対応
```

### 使用ガイドライン / Guidelines
- 新しいAPIエンドポイントの追加
- 新しいUIコンポーネントの実装
- 新しいデータベーステーブルの作成
- 新しいビジネスロジックの導入
- メジャーバージョンアップの原因となる機能追加（必ず`!`を使用）
- 既存機能の拡張（ただし、後方互換性を保つ場合）

### 注意事項 / Notes
- 破壊的変更を含む場合は必ず`feat!: ...`または`feat(api)!: ...`のように`!`を使用
- メジャーバージョンアップの基準となる新しい機能の追加

---

## fix

### 説明 / Description
バグ修正に関するコミットタイプです。コードの動作を修正し、既存の問題を解決します。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
fix: resolve login validation error
fix(api): fix null pointer exception in user service
fix(ui): correct button alignment on mobile devices
fix(database): fix data corruption during migration
fix(auth): fix session timeout issue
```

**日本語:**
```
fix: ログイン検証エラーを修正
fix(api): ユーザーサービスのヌルポインタ例外を修正
fix(ui): モバイルデバイスでのボタン配置を修正
fix(database): マイグレーション中のデータ破損を修正
fix(auth): セッションタイムアウト問題を修正
```

### 使用ガイドライン / Guidelines
- クラッシュやエラーの修正
- 意図しない動作の修正
- 辺境ケースの処理改善
- バグレポートの対応
- 既存機能の問題解決

### 注意事項 / Notes
- 機能追加ではなく、既存の問題を修正する場合のみ使用
- 重大なバグ修正でもパッチバージョン更新が原則

---

## docs

### 説明 / Description
ドキュメント関連の変更を表すコミットタイプです。ソースコードの変更を伴わない文書化の更新です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
docs: update API documentation
docs: add installation guide
docs: fix typo in README
docs: add contributing guidelines
docs: update deployment instructions
```

**日本語:**
```
docs: APIドキュメントを更新
docs: インストールガイドを追加
docs: READMEの誤字を修正
docs: 貢献ガイドラインを追加
docs: デプロイ手順を更新
```

### 使用ガイドライン / Guidelines
- READMEの更新
- APIドキュメントの追加・修正
- インストール手順の更新
- チュートリアルやガイドの追加
- コメントの改善
- READMEやCONTRIBUTING.mdなどの更新

### 注意事項 / Notes
- ドキュメントに記載されているコードの変更が必要な場合は、別途fixやfeatコミットを作成
- コード生成ツールが自動生成するドキュメントの更新には使用しない

---

## style

### 説明 / Description
コードのスタイルやフォーマットに関する変更を表すコミットタイプです。機能やロジックには影響しない変更です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
style: add missing semicolons
style: format code with Prettier
style: fix indentation
style: rename variable for clarity
style: remove unused imports
```

**日本語:**
```
style: 足りないセミコロンを追加
style: Prettierでコードを整形
style: インデントを修正
style: 可読性向上のため変数名を変更
style: 未使用のインポートを削除
```

### 使用ガイドライン / Guidelines
- コードのフォーマット整形
- インデントの調整
- 空白やタブの統一
- コーディング規約の適用
- 変数名や関数名のリファクタリング（ロジック変更なし）
- リンターによる警告の解消

### 注意事項 / Notes
- 機能やロジックに影響がない変更にのみ使用
- ESLintやPrettierなどの自動整形ツールでの変更に適したタイプ

---

## refactor

### 説明 / Description
コードのリファクタリング（内部構造の改善）を表すコミットタイプです。外部からの見た目（APIや動作）は変わらない内部改善です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
refactor: extract common logic to utility function
refactor: optimize database queries
refactor: simplify conditional statements
refactor: improve code organization
refactor: rename internal methods for clarity
```

**日本語:**
```
refactor: 共通ロジックをユーティリティ関数に抽出
refactor: データベースクエリを最適化
refactor: 条件文を簡素化
refactor: コードの組織構造を改善
refactor: 可読性向上のため内部メソッドをリネーム
```

### 使用ガイドライン / Guidelines
- コードの整理・構造改善
- 重複コードの削除
- 複雑なロジックの簡素化
- 関数やクラスの再設計
- コード品質向上のための改善
- テスト容易性の向上

### 注意事項 / Notes
- 外部インターフェースや動作が変わらない内部改善
- 機能追加やバグ修正ではない純粋なリファクタリング

---

## perf

### 説明 / Description
パフォーマンス改善に関するコミットタイプです。処理速度、メモリ使用量、応答時間などのパフォーマンス向上を目指す変更です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
perf: optimize image loading performance
perf: implement caching for API responses
perf: reduce memory usage in large datasets
perf: improve database query performance
perf: optimize bundle size
```

**日本語:**
```
perf: 画像読み込みパフォーマンスを最適化
perf: APIレスポンスのキャッシュを実装
perf: 大規模データセットでのメモリ使用量を削減
perf: データベースクエリパフォーマンスを改善
perf: バンドルサイズを最適化
```

### 使用ガイドライン / Guidelines
- アルゴリズムの最適化
- キャッシュ戦略の導入
- リソース使用効率の改善
- レスポンス時間の短縮
- メモリ使用量の削減
- バンドルサイズの削減

### 注意事項 / Notes
- パフォーマンス改善効果を計測可能にすること
- プロファイリングツールでの結果を確認してからコミット

---

## test

### 説明 / Description
テスト関連の変更を表すコミットタイプです。ユニットテスト、インテグレーションテスト、E2Eテストなど、テストコードの追加や更新です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
test: add unit tests for user service
test: fix failing test cases
test: add integration tests for API endpoints
test: update test data
test: add test coverage for edge cases
```

**日本語:**
```
test: ユーザーサービスのユニットテストを追加
test: 失敗していたテストケースを修正
test: APIエンドポイントのインテグレーションテストを追加
test: テストデータを更新
test: 辺境ケースのテストカバレッジを追加
```

### 使用ガイドライン / Guidelines
- 新しいテストの追加
- 既存テストの修正
- テストカバレッジの向上
- テストデータの改善
- テスト環境のセットアップ
- テストフレームワークの移行

### 注意事項 / Notes
- 実装コードに影響を与えないテストコードの変更
- CI/CDパイプラインでの実行に影響する場合があるため注意

---

## build

### 説明 / Description
ビルドシステムや依存関係に関する変更を表すコミットタイプです。ビルドプロセス、パッケージマネージャー、コンパイラ設定などに関する変更です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
build: update webpack configuration
build: upgrade to npm v8
build: add new development dependency
build: configure TypeScript compiler options
build: optimize build process
```

**日本語:**
```
build: webpack設定を更新
build: npm v8にアップグレード
build: 新しい開発依存関係を追加
build: TypeScriptコンパイラオプションを設定
build: ビルドプロセスを最適化
```

### 使用ガイドライン / Guidelines
- package.jsonの更新
- ビルドツールのバージョンアップ
- コンパイラ設定の変更
- 依存関係の追加・削除
- ビルドスクリプトの変更
- ビルドパイプラインの改善

### 注意事項 / Notes
- 開発環境やビルド環境に影響する変更
- 実行時動作に影響しないインフラ関連の変更

---

## ci

### 説明 / Description
CI/CD（継続的インテグレーション/継続的デプロイメント）に関する変更を表すコミットタイプです。GitHub Actions、Jenkins、CircleCIなどの設定変更です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
ci: add GitHub Actions workflow for testing
ci: configure automated deployment
ci: add security scanning
ci: update CI pipeline configuration
ci: add code quality checks
```

**日本語:**
```
ci: テスト用のGitHub Actionsワークフローを追加
ci: 自動デプロイを設定
ci: セキュリティスキャンを追加
ci: CIパイプライン設定を更新
ci: コード品質チェックを追加
```

### 使用ガイドライン / Guidelines
- CIワークフローの追加・修正
- デプロイパイプラインの設定
- 自動テストの設定
- コード品質チェックの導入
- セキュリティスキャンの追加
- ビルド・テスト・デプロイの自動化改善

### 注意事項 / Notes
- 開発ワークフローに影響するが、プロダクトコードに影響しない変更
- セキュリティ設定の変更には注意が必要

---

## chore

### 説明 / Description
その他の変更を表すコミットタイプです。ソースコード、テスト、ドキュメント、ビルド、CIのいずれにも分類されない雑多な変更です。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
chore: clean up temporary files
chore: update .gitignore
chore: organize project structure
chore: remove deprecated code
chore: update project metadata
```

**日本語:**
```
chore: 一時ファイルを掃除
chore: .gitignoreを更新
chore: プロジェクト構造を整理
chore: 非推奨のコードを削除
chore: プロジェクトメタデータを更新
```

### 使用ガイドライン / Guidelines
- 開発環境の整理
- プロジェクト設定ファイルの更新
- 不要ファイルの削除
- テンプレートファイルの更新
- プロジェクトメタデータの更新
- 非推奨コードの削除

### 注意事項 / Notes
- 明確に分類できない変更の最終手段
- 可能な限り他の適切なタイプを選択すること

---

## revert

### 説明 / Description
以前のコミットを取り消すことを表すコミットタイプです。過去の変更を元に戻す場合に使用します。

### SemVer レベル
**PATCH**

### 使用例 / Examples

**英語:**
```
revert: "feat: add new user interface"
revert: "fix: resolve authentication issue"
revert: "Remove unnecessary code from previous commit"
```

**日本語:**
```
revert: "feat: 新しいユーザーインターフェースを追加"
revert: "fix: 認証問題を解決"
revert: "前回のコミットから不要なコードを削除"
```

### 使用ガイドライン / Guidelines
- 間違ったコミットの取り消し
- 実装ミスの修正
- 方針転換による変更の撤回
- テスト目的での変更の取り消し

### 注意事項 / Notes
- Gitのrevertコマンドで自動生成されるコミットメッセージに従う
- 取り消すコミットのハッシュをメッセージに含めるのが望ましい

---

## Breaking Changes

### 説明 / Description
破壊的変更（後方互換性がない変更）を明示的に示すための記述方法です。

### SemVer レベル
**MAJOR**

### 使用方法 / Usage Method
コミットメッセージの本文に`BREAKING CHANGE:`セクションを追加するか、コミットタイプに`!`を付加します。

#### 方法1: ヘッダーに`!`を付加

**英語:**
```
feat!: add new authentication system with breaking changes
feat(api)!: remove deprecated user endpoint
fix!: completely rewrite database schema
```

**日本語:**
```
feat!: 破壊的変更を伴う新しい認証システムを追加
feat(api)!: 非推奨になったユーザーエンドポイントを削除
fix!: データベーススキーマを完全に書き換え
```

#### 方法2: 本文に`BREAKING CHANGE:`を追加

**英語:**
```
feat: add new authentication system

BREAKING CHANGE: The old authentication method is completely removed.
Users must use the new OAuth2 flow.

Closes #123
```

**日本語:**
```
feat: 新しい認証システムを追加

BREAKING CHANGE: 古い認証方法は完全に削除されました。
ユーザーは新しいOAuth2フローを使用する必要があります。

#123を閉じる
```

### 対象となる変更 / Target Changes
以下のような変更は破壊的変更とみなされます：

- APIエンドポイントの削除または変更
- データベーススキーマの変更
- 環境変数名の変更
- 設定ファイル形式の変更
- メソッドやクラスのシグネチャ変更
- 依存関係のバージョンアップ（メジャーバージョン）

### 注意事項 / Notes
- 破壊的変更があるコミットは必ずメジャーバージョンアップ
- 影響範囲を明確に文書化すること
- 代替手段や移行ガイドを提供すること

---

## まとめ / Summary

このリファレンスは、Conventional Commits規約に従ったコミットメッセージの作成を支援するためのもので、以下の要素を含みます：

- **コミットタイプ** - 変更の種類を分類
- **SemVerレベル** - バージョン管理方針
- **実例** - 実際の使用例（英語・日本語）
- **ガイドライン** - 適切な使用方法
- **注意事項** - トラブルを避けるためのポイント

適切なコミットタイプを選択することで、プロジェクトの変更履歴がより明確になり、チーム開発とバージョン管理が効率的になります。