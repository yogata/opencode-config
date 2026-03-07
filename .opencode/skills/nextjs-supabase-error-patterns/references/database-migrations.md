# データベース・マイグレーション関連

## 1. Migration ファイル名の番号衝突

**エラー症状:**
新しいマイグレーションファイルを作成した際、既存のファイル名と番号が重複

**エラー例:**
```
v2/supabase/migrations/
  001_init.sql
  002_add_staff_id.sql  (既存)
  002_add_serial_id.sql   (❌ 衝突)
```

**対処法:**
```
v2/supabase/migrations/
  001_init.sql
  002_add_staff_id.sql
  007_add_serial_id.sql  (✅ 連続した番号を使用)
```

**学び:**
- マイグレーションファイル名は一意なプレフィックス（数値）を持たせる
- 衝突する場合は、次の使用可能な番号を使用する
- 必要であれば既存のファイルをリネームして整理する

---

## 2. upsert の onConflict 設定ミス

**エラー症状:**
Supabase の upsert で予期せぬデータ重複または更新失敗

**エラー例:**
```javascript
// ❌ エラーになるコード
await supabase.from('schedules').upsert(batch, {
  onConflict: 'shop_id,staff_id,date',  // shop_id は schedules テーブルには存在しない
  ignoreDuplicates: false,
});
```

**対処法:**
```javascript
// ✅ 修正後
await supabase.from('schedules').upsert(batch, {
  onConflict: 'staff_id,date',  // テーブルのユニークキーを正確に指定
  ignoreDuplicates: false,
});
```

**学び:**
- onConflict はテーブルのユニーク制約と完全に一致させる必要がある
- テーブルスキーマを確認し、正しいカラムを指定する
- 外部キー（shop_id など）がテーブルに含まれていない場合がある

---

## 3. スキーマ変更後のカラム参照エラー

**エラー症状:**
データベーススキーマを変更した後、コードから削除済みカラムを参照しているとエラー発生

**エラー例:**
```javascript
// ❌ エラーになるコード（staff_name カラムは削除済み）
allSchedules.push({
  date: dateStr,
  staff_id: person.staffId || null,
  staff_name: person.name,  // ❌ カラムは削除されている
  start_time: person.time?.start || null,
  end_time: person.time?.end || null,
});
```

**対処法:**
```javascript
// ✅ 修正後（削除済みカラムを除去）
allSchedules.push({
  date: dateStr,
  staff_id: person.staffId || null,
  start_time: person.time?.start || null,
  end_time: person.time?.end || null,
});
```

**学び:**
- スキーマ変更後は、全ての参照コードをレビューして削除済みカラムを除去する
- テーブルスキーマの変更とコードの変更を同期させる
