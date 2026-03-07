# Next.js 16 関連のエラー

## 1. cookies() が async 関数であることへの対応

**エラー症状:**
Next.js 16 で `cookies()` を直接呼び出すと警告またはエラーが発生

**エラー例:**
```typescript
// ❌ エラーになるコード
cookies().set('auth-token', 'true', {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  path: '/',
});
```

**対処法:**
```typescript
// ✅ 修正後
const cookieStore = await cookies();
cookieStore.set('auth-token', 'true', {
  httpOnly: true,
  secure: process.env.NODE_ENV === 'production',
  sameSite: 'strict',
  path: '/',
});
```

**学び:**
- Next.js 16 から `cookies()` は async 関数として扱う必要がある
- `await cookies()` と記述し、その結果を使用する

---

## 2. useSearchParams を使用するページの Suspense boundary

**エラー症状:**
`useSearchParams` フックを使用するページで Next.js 16 の警告が発生

**エラー例:**
```typescript
// ❌ エラーになるコード
export default function HistoryPage() {
  return <HistoryContent />;  // useSearchParams 使用中
}
```

**対処法:**
```typescript
// ✅ 修正後
import { Suspense } from 'react';

export default function HistoryPage() {
  return (
    <Suspense fallback={
      <div className="min-h-screen bg-zinc-900 flex items-center justify-center">
        <div className="text-zinc-400">読み込み中...</div>
      </div>
    }>
      <HistoryContent />
    </Suspense>
  );
}
```

**学び:**
- `useSearchParams` フックを使用するコンポーネントは `Suspense` boundary でラップする必要がある
- fallback コンポーネントにはローディング表示を提供する
