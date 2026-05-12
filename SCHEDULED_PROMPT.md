# Daily IT News — Scheduled Task Prompt

このファイルは claude.ai/code/scheduled に登録するプロンプトのマスターコピーです。

---

## 登録プロンプト本文

```
今日の日付を取得し、直近24時間以内のIT関連ニュースを収集してGitHub Pages（asutarisucu/DailyNews）を更新してください。

## 手順

### 1. ニュース収集
Web検索で以下を優先順に収集（合計12件以上）：
- 【最優先】Shopify 開発者向け更新情報（Shopify Changelog, Shopify Engineering Blog, Dev.to/shopify）
- AI・機械学習（OpenAI, Google, Meta, Anthropic 等）
- セキュリティ（CVE, CISA, 主要ベンダーの脆弱性情報）
- クラウド（AWS, GCP, Azure のアップデート）
- 開発ツール・OSS（GitHub, npm, Vercel, Deno 等）
- その他ITトピック

各ニュースで必ず収集する情報：
- タイトル（日本語訳付き）
- ソースURL
- 要約（日本語、2〜4文）
- カテゴリ（shopify / ai / security / cloud / dev / other）

### 2. HTMLファイルの生成

#### docs/latest/news_YYYY-MM-DD.html（当日分詳細ページ）
- docs/assets/style.css を参照するパスは `../assets/style.css`
- Shopify記事を最上部の専用セクションに配置
- 他はカテゴリ別セクションに分類
- 各カードにタイトル・ソースURL・バッジ・要約を含める
- フッターに記事数・更新時刻を記載

#### docs/index.html の archives 配列を更新
index.html 内の JavaScript の archives 配列に当日エントリを先頭追加：
  { date: "YYYY-MM-DD", count: N, file: "latest/news_YYYY-MM-DD.html" }

### 3. 古いファイルの削除
docs/latest/ 配下で作成から7日以上経過した news_*.html を削除する。

### 4. Discord Webhook 通知
環境変数 DISCORD_WEBHOOK_URL が設定されている場合、更新完了後に以下のJSONをPOSTする：
```json
{
  "username": "Daily IT News",
  "avatar_url": "https://raw.githubusercontent.com/asutarisucu/DailyNews/main/docs/assets/icon.png",
  "embeds": [{
    "title": "📰 {DATE} のIT ニュースダイジェスト",
    "url": "https://asutarisucu.github.io/DailyNews/latest/news_{DATE}.html",
    "color": 7012095,
    "description": "本日のニュース {COUNT} 件を公開しました。",
    "fields": [
      { "name": "🟢 Shopify", "value": "{SHOPIFY_COUNT} 件", "inline": true },
      { "name": "🤖 AI", "value": "{AI_COUNT} 件", "inline": true },
      { "name": "🔒 Security", "value": "{SECURITY_COUNT} 件", "inline": true }
    ],
    "footer": { "text": "Daily IT News — 毎朝7時更新" },
    "timestamp": "{ISO_TIMESTAMP}"
  }]
}
```

### 5. git コミット & プッシュ
```bash
git add docs/
git commit -m "news: {DATE} のITニュースダイジェスト ({COUNT}件)"
git push origin main
```

## 注意事項
- すべてのテキストは日本語で出力する
- ソースURLは必ずオリジナル記事へのリンクにする
- HTMLはdocs/assets/style.cssのCSSクラスを使用する（新規インラインスタイル禁止）
- コミットメッセージは英語プレフィックス + 日本語で統一する
```
