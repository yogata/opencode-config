# ミドルウェアの実装

## 1. パス保護の実装

**エラー症状:**
ミドルウェアファイルが存在せず、パス保護が不十分

**対処法:**
```typescript
// ✅ src/middleware.ts を作成
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  const authHeader = request.headers.get('authorization');
  const cookieHeader = request.headers.get('cookie');

  // 認証チェック
  if (!cookieHeader?.includes('auth-token=true')) {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/admin/:path*', '/api/admin/:path*'],
};
```

**学び:**
- ミドルウェアファイルを作成し、パス保護を実装する
- `matcher` で保護するパスを指定する

---

## 2. Next.js 16 のプロキコンベンションへの移行

**エラー症状:**
Next.js 16 で `middleware.ts` が正しく機能しない、またはルーティングの問題が発生

**エラー例:**
```
src/
  middleware.ts  // ❌ この場所では動作しない（Next.js 16）
  app/
    admin/
    api/
```

**対処法:**
```
src/
  middleware.ts  // ✅ src/ 直下に配置（Next.js 16 プロキコンベンション）
  app/
    admin/
    api/
```

または、プロジェクトルートに配置:
```
middleware.ts  // ✅ プロジェクトルートも可
src/
  app/
```

**学び:**
- Next.js 16 からミドルウェアのプロキコンベンションが推奨されている
- ミドルウェアは `middleware.ts` として `src/` ディレクトリまたはプロジェクトルートに配置する
- `app/` ディレクトリ内に配置すると正しく機能しない
- ミドルウェアの `config.matcher` で適用するパスを明示的に指定する
