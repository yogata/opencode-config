# スクレイパー・データ処理関連

## 1. 日付計算のロジックミス

**エラー症状:**
日付範囲の計算で誤った関数を使用し、意図しない日付範囲を取得

**エラー例:**
```javascript
// ❌ エラーになるコード（subDays は過去の日付に遡る）
const today = new Date();
const startDate = subDays(today, MIN_ADD_DAY);  // ❌ 負数の場合未来になる
```

**対処法:**
```javascript
// ✅ 修正後（addDays を使用）
const today = new Date();
const startDate = addDays(today, MIN_ADD_DAY);  // ✅ 負数の場合過去になる
```

**学び:**
- `addDays(date, days)` は days が正で未来、負で過去
- `subDays(date, days)` は常に過去へ遡る
- 日付範囲の計算には `addDays` を一貫して使用する

---

## 2. 正規表現の桁数制限

**エラー症状:**
年齢抽出の正規表現で、予期せぬ4桁以上の数値をキャッチ

**エラー例:**
```javascript
// ❌ 問題のあるコード（4桁以上もキャッチ）
function extractAge(name) {
  const match = name.match(/[（(](\d{1,3})[）)]$/);
  return match ? parseInt(match[1], 10) : null;
}
// （1234）→ 1234 になってしまう（年齢として異常値）
```

**対処法:**
```javascript
// ✅ 修正後（4桁以上を除外）
function extractAge(name) {
  const match = name.match(/[（(](\d{1,3})[）)]$/);
  if (match && match[1].length > 3) {  // 4桁以上のパターンを除外
    return null;
  }
  return match ? parseInt(match[1], 10) : null;
}
```

**学び:**
- 正規表現でキャッチした値の長さをチェックし、異常値を除外する
- 年齢など妥当な範囲（1-3桁）を明示的に定義する

---

## 3. 重複したコードの削除

**エラー症状:**
関数定義が重複しており、コードが読みにくい

**エラー例:**
```javascript
// ❌ 問題のあるコード（重複した関数定義）
function parsePeopleFromHtml(html) {
  // ...処理コード...
  return people;
}

    const sizeElem = $(elem).find('.main_section_info_size').first();
    const sizeText = sizeElem.text().trim();
    // ...重複した処理コード...

  return people;  // ❌ 重複した return
}
```

**対処法:**
```javascript
// ✅ 修正後（重複部分を削除）
function parsePeopleFromHtml(html) {
  // ...処理コード...
  return people;
}
```

**学び:**
- コードレビューで重複した関数定義や処理コードを見つけた場合、削除する
- コードの重複は保守性を低下させる
