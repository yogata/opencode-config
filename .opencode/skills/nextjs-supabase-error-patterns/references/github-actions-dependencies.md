# CI/CD 関連

## 1. MODULE_NOT_FOUND error for dotenv

**エラー症状:**
GitHub Actions で依存関係のインストール時に dotenv が見つからないエラーが発生

**エラー例:**
```
Error: Cannot find module 'dotenv'
```

**原因:**
`dotenv` が `devDependencies` に記述されており、GitHub Actions 環境ではインストールされない

**エラー例:**
```json
{
  "dependencies": {
    "supabase": "^2.45.0",
    "next": "16.0.0"
  },
  "devDependencies": {
    "dotenv": "^16.4.0"  // ❌ GitHub Actions ではインストールされない
  }
}
```

**対処法1（推奨）: dotenv を dependencies に移動**
```json
{
  "dependencies": {
    "dotenv": "^16.4.0",  // ✅ dependencies に移動
    "supabase": "^2.45.0",
    "next": "16.0.0"
  },
  "devDependencies": {}
}
```

**対処法2: GitHub Actions で devDependencies もインストール**
```yaml
# .github/workflows/ci.yml
- name: Install dependencies
  run: npm ci  # ❌ devDependencies はインストールされない
```

```yaml
# ✅ 修正後
- name: Install dependencies
  run: npm ci --include=dev  # devDependencies もインストール
```

**対処法3: --production=false を使用**
```yaml
# ✅ 修正後（npm install を使用する場合）
- name: Install dependencies
  run: npm install --production=false
```

**学び:**
- `devDependencies` は開発環境のみで使用されるパッケージ用
- GitHub Actions などの本番環境では `npm ci` を使用すると devDependencies はインストールされない
- 本番環境で使用するパッケージ（dotenvなど）は `dependencies` に記述する
- `npm ci --include=dev` または `npm install --production=false` で devDependencies もインストール可能

---

## 2. 依存関係のインストール効率化

**エラー症状:**
GitHub Actions で依存関係のインストールに時間がかかりすぎる

**エラー例:**
```yaml
# ❌ 非効率的なインストール
- name: Install dependencies
  run: npm install
```

**対処法:**
```yaml
# ✅ 効率的なインストール
- name: Install dependencies
  run: npm ci
```

**学び:**
- `npm install` は package-lock.json を更新する可能性があり、再現性に欠ける
- `npm ci` は package-lock.json から正確にインストールし、再現性が高い
- `npm ci` は依存関係の解決が高速で、CI/CD に適している
