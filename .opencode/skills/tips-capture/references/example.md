# Example Workflow

**シナリオ**: SupabaseのRLSポリシーでエラーが発生し、解決した

### Step 1: 学びの検知

- RLSポリシーの設定ミスが原因
- `USING (auth.uid() = user_id)` の書き方を学んだ
- 将来同じミスを防ぐために記録すべき

### Step 2: 学びの抽出

```
## Supabase RLSポリシーのauth.uid()比較

**状況**: SupabaseでRLSポリシーを設定した際、SELECTで権限エラー
**原因**: `auth.uid()` と `user_id` の比較で、カラム名を間違えていた
**解決策**: テーブルのカラム名と一致しているか確認。`auth.uid()` は UUID を返す
**教訓**: RLSポリシー作成時は、カラム名と型を確認する
**タグ**: #supabase #rls #バグ修正
```

### Step 3: ユーザー確認

> 今回のRLS対応で学びがありました。追加しますか？
>
> ## Supabase RLSポリシーのauth.uid()比較
> ...
>
> - はい / いいえ

### Step 4: 学びの追加

ユーザーが「はい」と回答：

```
/tips-add "Supabase RLSポリシーのauth.uid()比較" --tags "supabase,rls,バグ修正"
```

これで `inbox.md` に学びが追加されます。

---

## Full Pipeline Example (Complete 3-Layer Flow)

### Layer 1: Capture Phase (学びの記録)

```
/tips-add "Supabase RLSポリシーのauth.uid()比較" --tags "supabase,rls,バグ修正"
/tips-add "Next.jsのISRでキャッシュ更新" --tags "nextjs,キャッシュ"
/tips-add "TypeScriptの型推論" --tags "typescript,型システム"
```

→ `inbox.md` にエントリが追加される

### Layer 2: Analysis Phase (セマンティック分析と整理)

```
/tips-refactor
```

→ 実行内容:
  - inbox.md のエントリをセマンティック分析で自動分類
  - evaluation-report.md に品質評価と分類結果を生成
  - archive.md にトピック別に整理して保存
  - inbox.md はクリア

→ 出力例 (evaluation-report.md):
  - **トピック: Supabase/RLS** (3エントリ) - 品質評価: 高
  - **トピック: Next.js/キャッシュ** (2エントリ) - 品質評価: 中
  - **トピック: TypeScript/型システム** (4エントリ) - 品質評価: 高

### Layer 3: Elevation Phase (ステージング候補の判定)

```
/tips-elevate
```

→ 実行内容:
  - evaluation-report.md を分析
  - 高品質で十分な量のトピックを判定
  - ステージング用のスタブファイルを作成

→ 出力例 (staging/Supabase-RLS.md):
  ```markdown
  # Supabase RLSポリシーに関する学び

  エントリ数: 3
  品質評価: 高

  ## 概要
  ...

  ## 詳細
  ...
  ```
