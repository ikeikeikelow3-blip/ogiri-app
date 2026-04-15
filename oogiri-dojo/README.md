# 🎤 大喜利道場

AIが採点する大喜利トレーニングアプリ

## デプロイ手順（Vercel）

### 1. リポジトリをGitHubにプッシュ

```bash
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/YOUR_USER/oogiri-dojo.git
git push -u origin main
```

### 2. Vercelにインポート

1. [vercel.com](https://vercel.com) にログイン
2. 「Add New Project」→ GitHubリポジトリを選択
3. そのまま「Deploy」

### 3. 環境変数を設定

Vercelダッシュボード → Settings → Environment Variables で以下を追加：

| Name | Value |
|------|-------|
| `ANTHROPIC_API_KEY` | `sk-ant-...`（あなたのAPIキー） |

設定後、**Redeploy**してください。

## プロジェクト構成

```
oogiri-dojo/
├── public/
│   └── index.html      ← フロントエンド（単一HTML）
├── api/
│   └── score.js        ← Vercel Serverless Function（API proxy）
├── vercel.json         ← ルーティング設定
├── package.json
└── README.md
```

## 技術構成

| 項目 | 技術 |
|------|------|
| フロントエンド | Vanilla HTML/CSS/JS（単一ファイル） |
| バックエンド | Vercel Serverless Functions |
| AI採点 | Anthropic Claude API（サーバー経由） |
| データ保存 | localStorage（端末ごと） |
| スマホ対応 | レスポンシブ + safe-area + タッチ最適化 |

## 主な機能

- ログイン画面（芸名入力）
- 8つのお題からランダム選択
- AIによる8項目の多面的採点（100点満点）
- ランキング（TOP10、端末内保存）
- スマホファースト設計

## 注意事項

- `ANTHROPIC_API_KEY` が未設定だと簡易採点（ランダム）になります
- ランキングは `localStorage` に保存されるため端末ごとのデータです
- 複数端末で共有する場合はデータベース（Vercel KV等）の導入が必要です
