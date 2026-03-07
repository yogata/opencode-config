# 認証・認可関連

## 1. WWW-Authenticate ヘッダーの問題

**エラー症状:**
認証失敗時に WWW-Authenticate ヘッダーを返すと、ブラウザが認証プロンプトを表示

**エラー例:**
```typescript
// ❌ 問題のあるコード（Basic認証廃止後でも残存）
export function middleware(request: NextRequest) {
  if (!isValidAuth(authHeader)) {
    return new NextResponse('Unauthorized', {
      status: 401,
      headers: {
        'WWW-Authenticate': 'Basic realm="Secure Area"',  // ❌ ブラウザプロンプト
      },
    });
  }
}
```

**対処法:**
```typescript
// ✅ 修正後（ヘッダーを削除）
export function middleware(request: NextRequest) {
  if (!isValidAuth(authHeader)) {
    return new NextResponse('Unauthorized', {
      status: 401,
      // headers を削除（ブラウザプロンプトを回避）
    });
  }
}
```

**学び:**
- Basic認証をセッションベース認証に移行した場合、WWW-Authenticate ヘッダーを削除する
- ブラウザのデフォルト認証プロンプトを回避することで、独自のUIを表示できる

---

## 2. Basic認証のセキュリティ問題

**エラー症状:**
Basic認証はセキュリティ上の懸念がある（中間者攻撃への脆弱性）

**推奨される対処法:**
```typescript
// ✅ 推奨されるセッションベース認証への移行
export async function POST(request: Request) {
  const { username, password } = await request.json();

  // 認証成功時に HttpOnly cookie を発行
  const cookieStore = await cookies();
  cookieStore.set('auth-token', 'true', {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict',
    path: '/',
    maxAge: 60 * 60 * 24,  // 24時間
  });

  // Basic認証ヘッダーは使用しない
}
```

**学び:**
- Basic認証は平文パスワードを送信するため、セキュリティ上の懸念がある
- HttpOnly cookie を使用したセッションベース認証への移行を推奨
- HTTPS の使用とトークンの有効期間短縮でセキュリティを強化

---

## 3. ハードコーディングされた認証情報の削除

**エラー症状:**
コード内に認証情報（APIキー、パスワードなど）がハードコーディングされ、セキュリティリスクとなる

**エラー例:**
```typescript
// ❌ 問題のあるコード（認証情報がハードコーディングされている）
const SUPABASE_URL = 'https://xxx.supabase.co';
const SUPABASE_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...'; // ハードコーディングされたキー
```

**対処法:**
```typescript
// ✅ 修正後（環境変数を使用）
const SUPABASE_URL = process.env.NEXT_PUBLIC_SUPABASE_URL!;
const SUPABASE_KEY = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!;
```

**.env.example**（バージョン管理に含める）:
```bash
NEXT_PUBLIC_SUPABASE_URL=your_supabase_project_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
```

**.gitignore**（バージョン管理から除外）:
```
.env
.env.local
.env.*.local
```

**学び:**
- 認証情報をハードコーディングすると、リポジトリ公開時に漏洩するリスクがある
- 環境変数（.envファイル）を使用して機密情報を管理する
- .env.example を提供して開発者が必要な環境変数を把握できるようにする
- .gitignore で .env ファイルをバージョン管理から除外する
