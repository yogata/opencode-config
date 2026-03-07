# TypeScript のスコープ問題

## 1. try-catch ブロックのスコープ問題

**エラー症状:**
try ブロック内で宣言した変数を catch ブロックで参照するとビルドエラーが発生

**エラー例:**
```typescript
// ❌ エラーになるコード
async function fetchShops() {
  try {
    const response = await fetch('/api/shops');
    const statusCode = response.status;  // try 内で宣言
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${statusCode}`);
    }
  } catch {
    console.log(statusCode);  // ❌ ここでは参照できない
  }
}
```

**対処法:**
```typescript
// ✅ 修正後
async function fetchShops() {
  let statusCode: number | undefined;  // try 外で宣言

  try {
    const response = await fetch('/api/shops');
    statusCode = response.status;
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${statusCode}`);
    }
  } catch {
    console.log(statusCode);  // ✅ ここで参照できる
  }
}
```

**学び:**
- try-catch ブロックをまたいで使用する変数は、try 外で宣言する
- 型アノテーションを明示的に付与する
