---
description: evaluation-report.mdとarchive.mdから昇華判定を行い、staging領域にスタブを生成する
agent: sisyphus
load_skills:
  - tips-capture
---

# 学びの昇華判定とスタブ生成

`docs/tips/evaluation-report.md` の問題クラスラスタを主入力とし、`docs/tips/archive.md` の過去エントリを参照して廃棄判定を行う。ユーザー承認後に `docs/tips/elevation-staging/` にスタブファイルを生成し、処理済みエントリを archive.md から pruning する。

**重要**: `.opencode/` への直接配置は行わない。生成したスタブは `issue-req → issue-save-req → issue-create → issue-work` のルートで実装に移行する。

## Steps

1. **evaluation-report.mdの存在確認**:
   - `docs/tips/evaluation-report.md` を確認
   - 存在しない → エラー終了。「先に `/tips-refactor` を実行して分析レポートを生成してください」

2. **データ読込**:
   - evaluation-report.md を読込（クラスタ一覧・テーマ概要・重み・エントリ数を把握）
   - archive.md を読込（該当クラスタの過去エントリの日付・タイトル・内容を確認）

3. **廃棄判定**（11カテゴリ + duplicate）:
   - **主入力**: evaluation-report.md の問題クラスラスタ（raw tips の再分類は禁止）
   - 各クラスタに対し、以下の11カテゴリ + duplicate から最適な廃棄先を判定

   ### 廃棄カテゴリ一覧

   | # | カテゴリ | 判定基準 |
   |---|---------|---------|
   | 1 | 既存 command へ反映 | 既存コマンドのステップ・ガードレール・エラーハンドリングに追加すべき手順・制約 |
   | 2 | 既存 skill へ反映 | 既存スキルのPrerequisites/Steps/Guardrails/MUST NOTに追加すべき知見 |
   | 3 | 新規 skill 化 | 汎用的なパターン、複数プロジェクト/コンテキストで再利用可能、独立した判断・手順が確立 |
   | 4 | 新規 command 化 | 特定の操作フローが繰り返し現れている、自動化すべき手順が明確 |
   | 5 | template 反映 | ドキュメント・Issue・PR等のテンプレート形式に反映すべきフォーマット知見 |
   | 6 | ADR 候補 | アーキテクチャに関する設計判断・技術選定の理由を記録すべき内容 |
   | 7 | spec 候補 | システム仕様・実装パターン・設計原則として docs/specs/ に反映すべき内容 |
   | 8 | REQ 候補 | 要件変更・機能追加の要因となる知見、既存REQの更新が必要な内容 |
   | 9 | project-local knowledge | プロジェクト固有の落とし穴・環境依存の知見、汎用化が難しい内容 |
   | 10 | deferred | まだ昇華の余地がない、情報が断片的、出現回数が少ない |
   | 11 | rejected | ユーザーが明示的に却下、すでに別の対策で十分対応済み |
   | + | duplicate | 既存の command/skill/template/docs で既に同等の内容が十分にカバーされている |

   ### 反映先マッピング

   クラスタの性質に基づく反映先の決定:
   - **knowledge**（汎用知見）→ skill の Steps/Guardrails へ反映
   - **procedures**（手順）→ command の Step へ反映
   - **constraints**（制約・注意事項）→ command/skill の Guardrails/MUST NOT へ反映
   - **format**（フォーマット）→ template + command のフォーマット検証へ反映
   - **user-confirmed work**（ユーザー確認済み作業フロー）→ command workflow へ反映
   - **architecture**（アーキテクチャ決定）→ ADR 候補
   - **system spec**（システム仕様）→ docs/specs/ へ反映
   - **requirement change**（要件変更）→ REQ/Issue 更新
   - **project-specific pitfalls**（プロジェクト固有の落とし穴）→ project-local knowledge

4. **既存対策確認**（既存 measure マッチング）:
   - 各クラスタの内容に対し、既存の command/skill/template/docs に類似対策が存在するか確認
   - 確認対象:
     - `.opencode/commands/` 配下の全コマンド
     - `.opencode/skills/` 配下の全スキル
     - `.opencode/skills/issue-template-manager/templates/` 配下のIssue/コメントテンプレート
     - `.opencode/skills/req-file-manager/templates/`, `.opencode/skills/adr-file-manager/templates/`, `.opencode/skills/spec-compliance/templates/` 配下のドキュメント・レポートテンプレート
     - `docs/specs/` 配下の仕様書
     - `docs/adr/` 配下のADR
     - `docs/requirements/` 配下のREQ
   - **結果に基づく判定**:
     - 類似対策あり → 「既存Xへ反映」を「新規X化」より優先
     - 類似対策なし → 「新規X化」を検討
   - **ギャップの分類**（既存対策がある場合）:
     - **fix gap**: 対策内容に不備・欠落がある
     - **application miss**: 対策は存在するが適用されていないケースがある
     - **load miss**: 対策は存在するが該当コマンド/skillがロードされていない
     - **guardrail insufficiency**: ガードレール・MUST NOTが不十分

5. **ユーザーへの判定結果提示**:

   ```
   ## 昇華判定結果

   | クラスタ | テーマ | 廃棄判定 | 既存対策 | 理由 |
   |---------|--------|---------|---------|------|
   | 1 | Windows環境エスケープ問題 | 既存 command へ反映 | issue-work に部分的に対策あり（fix gap） | ガードレールが不十分、3回出現 |
   | 2 | Supabase RLS落とし穴 | 新規 skill 化 | なし | 汎用的パターン、独立した判断手順あり |
   | 3 | コミットメッセージ形式 | duplicate | conventional-commits skill で十分カバー | 既存skillで対応済み |
   | 4 | 環境変数管理の注意点 | deferred | なし | 情報が断片的、出現1回のみ |
   ```

   統計サマリ:
   - 昇華対象: N件（staged）
   - 保留: N件（deferred）
   - 却下・重複: N件（rejected/duplicate）

6. **ユーザー承認**:
   - ユーザーが各クラスタの廃棄判定を確認・修正
   - 判定の変更指示があれば Step 3〜4 を再実行
   - 承認したクラスタのみ処理
   - 承認しない → 「昇華をキャンセルしました」と報告して終了

7. **スタブ生成**（staging領域のみ）:
   - 出力先: `docs/tips/elevation-staging/`
   - ファイル名: `{disposal-category}-{name}.md`
     - 例: `existing-command-windows-escape.md`, `new-skill-supabase-rls.md`
   - **`.opencode/` への直接書込は禁止**
   - **`issue-work` への直接受け渡しは禁止**（`issue-req` を経由すること）

   ### スタブフォーマット（7つの必須フィールド）

   各スタブには以下の7項目を必ず含める:

   ```markdown
   # {name}

   ## 判定

   - **廃棄カテゴリ**: {11カテゴリ or duplicate}
   - **判定理由**: {なぜこのカテゴリに判定したか}

   ## 既存対策確認

   - **確認結果**: {既存対策あり/なし}
   - **該当ファイル**: {該当コマンド/skill/template/docsのパス、なしの場合は「なし」}
   - **ギャップ分類**: {fix gap / application miss / load miss / guardrail insufficiency / なし}
   - **ギャップ詳細**: {具体的な不備・欠落の内容、なしの場合は「なし」}

   ## 反映先

   - **反映先パス**: {反映先のファイルパス}
   - **反映先セクション**: {Steps/Guardrails/MUST NOT/Steps の新規セクション 等}

   ## 反映内容

   {何を反映するかの具体的な説明。ステップの追加、ガードレールの追加、セクションの新設等}

   ## 元tips

   - **要約**: {クラスタのテーマ概要}
   - **根拠**: {判定の根拠となった事象・原因・対策の要約}
   - **再発条件**: {同じ問題が再発する可能性のある条件}
   - **横展開可能性**: {他のプロジェクト/コンテキストでも発生しうるか}

   ## 実装方針

   {スタブから実装に移行する際の方針。issue-reqで要件化する際の指針を記載}

   ## 完了条件

   - [ ] {完了条件1}
   - [ ] {完了条件2}
   - [ ] {完了条件3}
   ```

   ### カテゴリ別の反映先パス例

   | カテゴリ | 反映先パス例 |
   |---------|-------------|
   | 既存 command へ反映 | `.opencode/commands/{target-command}.md` |
   | 既存 skill へ反映 | `.opencode/skills/{target-skill}/SKILL.md` |
   | 新規 skill 化 | `.opencode/skills/{new-skill}/SKILL.md` |
   | 新規 command 化 | `.opencode/commands/{new-command}.md` |
   | template 反映 | `.opencode/skills/issue-template-manager/templates/{template}.md` |
   | ADR 候補 | `docs/adr/ADR-{NNNN}-{name}.md` |
   | spec 候補 | `docs/specs/{spec-name}.md` |
   | REQ 候補 | `docs/requirements/REQ-{NNNN}-{name}.md` |
   | project-local knowledge | `docs/tips/project-knowledge.md` |

8. **昇華時 prune**（archive.md からの除去）:
   - **prune 対象**: staged（スタブ生成済み）/ rejected / duplicate のエントリのみ
   - **prune 非対象**: deferred / 未処理のエントリは archive.md に残す
   - **証拠保存**: staged エントリを除去する際、以下の情報をスタブの「元tips」セクションに保存してから除去:
     - 要約（クラスタのテーマ概要）
     - 根拠（事象・原因・対策の要約）
     - 再発条件
     - 横展開可能性
   - **実行手順**:
     1. prune 対象エントリの特定（staged/rejected/duplicate のクラスタに属するエントリ）
     2. ユーザーに prune 計画を提示:
        ```
        ## Prune 計画

        以下のエントリを archive.md から除去します:

        ### Staged（スタブ生成済み）
        - YYYY-MM-DD: タイトル → {スタブファイル名} に証拠保存済み
        - YYYY-MM-DD: タイトル → {スタブファイル名} に証拠保存済み

        ### Rejected
        - YYYY-MM-DD: タイトル

        ### Duplicate
        - YYYY-MM-DD: タイトル（{重複先} でカバー済み）

        ### 残存エントリ
        - deferred: N件
        - 未処理: N件
        ```
     3. ユーザーが prune を承認した場合のみ実行
     4. archive.md から該当エントリを除去（deferred・未処理は保持）

9. **完了報告**:
   - 生成したスタブファイル一覧（パス・カテゴリ・内容要約）
   - prune 結果（除去したエントリ数・残存エントリ数）
   - **次ステップの案内**:
     - 「生成したスタブは `issue-req` で要件化し、`issue-save-req` → `issue-create` → `issue-work` のルートで実装に移行してください」
     - 「`/issue/issue-req` に対象のスタブファイルパスを指定して開始できます」
   - **注意事項**:
     - `.opencode/` への直接配置は行わないこと
     - スタブは必ず `issue-req` を経由すること（`issue-work` に直接渡さないこと）

## Error Handling

| エラー | 対処 |
|--------|------|
| evaluation-report.mdが存在しない | エラー終了。「先に `/tips-refactor` を実行して分析レポートを生成してください」 |
| クラスタが0件 | 「昇華対象のクラスタがありません」と報告して終了 |
| ユーザーが承認しない | 「昇華をキャンセルしました」と報告して終了 |
| staging領域の書込失敗 | エラー内容を報告 |
| archive.mdの prune 失敗 | スタブ生成は保持。prune エラー内容を報告し、手動での prune を案内 |

## 使用例

```
/tips-elevate
→ evaluation-report.md の存在確認
→ evaluation-report.md + archive.md を読込
→ 各クラスタの廃棄判定（11カテゴリ + duplicate）
→ 既存 command/skill/template/docs への対策確認
→ 判定結果をテーブルで提示し、ユーザー承認を求める
→ 承認後、staging領域にスタブを生成（7つの必須フィールド）
→ staged/rejected/duplicate エントリを archive.md から prune
→ 完了報告（次ステップ: issue-req → issue-save-req → issue-create → issue-work）
```

## 注意事項

- **staging領域のみに生成**: `.opencode/skills/` や `.opencode/commands/` への直接配置は禁止
- **スタブのみ生成**: 完全なSKILL.mdやコマンドファイルは生成しない（7つの必須フィールドを持つスタブのみ）
- **evaluation-report.mdは読込専用**: 変更・削除は禁止
- **archive.md は living tips pool**: prune は staged/rejected/duplicate のみ。deferred・未処理は保持
- **ユーザー承認必須**: 判定・prune ともに承認なしに実行しない
- **主入力は evaluation-report.md**: raw tips の再分類は禁止
- **既存対策を優先**: 「新規X化」より「既存Xへ反映」を優先
- **反射ルート**: staging stub → `issue-req` → `issue-save-req` → `issue-create` → `issue-work`
- **elevation-ledger.md は生成しない**: 管理用ファイルは作成しない
