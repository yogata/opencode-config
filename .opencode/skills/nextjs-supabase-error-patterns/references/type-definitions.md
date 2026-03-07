# 型定義とコンポーネント設計

## 1. any型の乱用

**エラー症状:**
テストファイルで any 型が多用され、型安全性が低下

**エラー例:**
```typescript
// ❌ 問題のあるコード
describe('GET /api/shops', () => {
  it('should return list of shops', async () => {
    const response: any = await fetch('http://localhost:3000/api/shops');
    expect(response.ok).toBe(true);
    const data: any = await response.json();
    expect(data).toHaveLength(2);  // any型なので補完が効かない
  });
});
```

**対処法:**
```typescript
// ✅ 修正後（具体的な型を定義）
interface Shop {
  id: number;
  name: string;
  // ...その他のプロパティ
}

describe('GET /api/shops', () => {
  it('should return list of shops', async () => {
    const response = await fetch('http://localhost:3000/api/shops');
    expect(response.ok).toBe(true);
    const data: Shop[] = await response.json();
    expect(data).toHaveLength(2);  // ✅ 型補完が効く
  });
});
```

**学び:**
- テストファイルでも具体的な型定義を作成する
- any型は極力避け、型安全性を維持する

---

## 2. Props型の過剰な指定

**エラー症状:**
コンポーネントの Props に不要なプロパティを含め、型が厳密すぎる

**エラー例:**
```typescript
// ❌ 問題のあるコード
interface FilterFormProps {
  shops: Shop[];  // Shop の全プロパティが必要
}

export default function FilterForm({ shops }: FilterFormProps) {
  // shops は id と name しか使用していない
}
```

**対処法:**
```typescript
// ✅ 修正後（Pick型で必要なプロパティのみ指定）
interface FilterFormProps {
  shops: Pick<Shop, 'id' | 'name'>[];
}

export default function FilterForm({ shops }: FilterFormProps) {
  // shops は id と name のみ使用
}
```

**学び:**
- コンポーネントで使用するプロパティのみを Props に含める
- `Pick<Type, Keys>` を使用して、必要なプロパティのみを抽出する
