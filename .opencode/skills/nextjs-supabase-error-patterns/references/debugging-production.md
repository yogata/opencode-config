# デバッグと本番環境

## 1. デバッグログの制御

**エラー症状:**
本番環境でもデバッグログが出力され、パフォーマンスやセキュリティに影響

**対処法:**
```javascript
// ✅ 環境変数でログを制御
const DEBUG = process.env.NODE_ENV === 'development';

function debugLog(...args) {
  if (DEBUG) {
    console.log('[DEBUG]', ...args);
  }
}

// 使用時
debugLog(`解析後スタッフ数: ${people.length}`);
```

**学び:**
- デバッグログを環境変数で制御する
- 本番環境ではログレベルを調整し、不要なログを出力しない
