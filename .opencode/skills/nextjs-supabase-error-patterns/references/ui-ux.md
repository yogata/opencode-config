# UI/UX 改善

## 1. テーブルの列順序と幅調整

**エラー症状:**
テーブルの列が適切に表示されず、ユーザビリティが低下

**対処法:**
```typescript
// ✅ 列順序の調整（店舗 → スタッフ名）
<th className="px-4 py-3 text-left text-xs font-medium uppercase text-zinc-400 sticky left-0 bg-zinc-900 z-20 w-20 min-w-[5rem]">
  店舗
</th>
<th className="px-4 py-3 text-left text-xs font-medium uppercase text-zinc-400 sticky left-20 bg-zinc-900 z-20 w-24 min-w-[6rem] shadow-[2px_0_5px_-2px_rgba(0,0,0,0.1)]">
  スタッフ名
</th>
```

**学び:**
- テーブルの列順序をユーザビリティに合わせて調整する
- 列幅を適切に設定し、sticky ヘッダーを使用する

---

## 2. スクロール表示の改善

**エラー症状:**
長いテキストがセルを溢れ、スクロールが適切に表示されない

**対処法:**
```typescript
// ✅ truncate クラスを追加
<td className={`px-4 py-3 text-sm font-medium text-zinc-100 whitespace-nowrap sticky left-20 z-10 w-24 min-w-[6rem] max-w-[6rem] truncate border-r border-zinc-700 shadow-[2px_0_5px_-2px_rgba(0,0,0,0.1)] ${rowBgColor}`}>
  {staff.name}
</td>
```

**学び:**
- 長いテキストには `truncate` クラスを追加し、省略記号（...）を表示する
- `max-w-*` クラスで最大幅を設定する
