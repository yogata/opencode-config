---
name: obsidian-note-workflow
description: Manages Obsidian Vault note lifecycle (capture, organize, solidify, archive) with tagging and Zettelkasten principles. Use when creating notes, organizing inbox, adding tags, or archiving notes.
---

# Obsidian Note Workflow

## Overview
Obsidian Vaultの PARA × Zettelkasten 運用に基づくノート管理ワークフロー。収集・整理・定着・アーカイブのライフサイクルを管理します。

## When to Use
Obsidian Vaultでノートを作成・管理・整理する場合

## Core Pattern
収集 → 整理 → 定着 → アーカイブ

## Quick Reference
| ステージ | フォルダ | タグ | アクション |
|---------|----------|------|-----------|
| 収集 | 00_inbox | type/inbox | キャプチャ |
| 整理 | 40_resources/41_inbox | type/note | 分類・タグ付け |
| 定着 | 40_resources/42_notes | type/permanent | リンク付け |
| アーカイブ | 50_archives | status/archived | 移動 |

## Stages

### Capture (収集)
Capture ideas, bookmarks, and fleeting notes into `00_inbox` with `type/inbox` tag. No formatting required — raw capture is the priority. Process later during organizing stage.

### Organize (整理)
Move notes from `00_inbox` to `40_resources/41_inbox`, apply appropriate tags (`type/note`, domain tags), and assign a descriptive title. Each note should have one clear purpose.

### Solidify (定着)
Transform organized notes into permanent notes in `40_resources/42_notes` with `type/permanent` tag. Add bidirectional links to related notes, refine content for long-term value, and ensure atomicity (one idea per note).

### Archive (アーカイブ)
Move completed or inactive notes to `50_archives` with `status/archived` tag. Notes remain searchable but out of active workspace. Review periodically for reactivation.

## Implementation
各ステージの詳細手順と具体例は、references/ ディレクトリの各ファイルを参照:
- [note-lifecycle.md](references/note-lifecycle.md): ライフサイクル詳細
- [tag-guidelines.md](references/tag-guidelines.md): タグ設計ガイドライン
- [link-patterns.md](references/link-patterns.md): リンク付けパターン

## Common Mistakes
- **Too many tags**: Adding 5+ tags to a single note creates noise. Limit to 2-3 meaningful tags per note. Example: ❌ `#idea #project #work #important #urgent` → ✅ `#project/alpha type/note`
- **Orphan notes without links**: Notes without any `[[wiki-links]]` become undiscoverable. Always link new notes to at least one existing note. Example: ❌ A standalone meeting note → ✅ Meeting note linked to project note and person note
- **Incomplete organization**: Leaving notes in `00_inbox` after processing. Once reviewed and tagged, move to the appropriate stage folder immediately
