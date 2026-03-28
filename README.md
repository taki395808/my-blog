# dev.log — 個人ブログ・学習記録サイト

エンジニアの個人ブログサイトです。
Astro + Tailwind CSS で構築し、GitHub Pages でホスティングします。

## 技術スタック

| カテゴリ | 技術 |
|---------|------|
| フレームワーク | [Astro](https://astro.build) v6 |
| スタイリング | [Tailwind CSS](https://tailwindcss.com) v4 |
| コンテンツ | Markdown / MDX |
| ホスティング | GitHub Pages |
| CI/CD | GitHub Actions |

## ローカル開発

```bash
# 依存関係のインストール
npm install

# 開発サーバー起動（http://localhost:4321）
npm run dev

# 本番ビルド
npm run build

# ビルド結果のプレビュー
npm run preview
```

## ディレクトリ構成

```text
my-blog/
├── src/
│   ├── pages/
│   │   ├── index.astro          # ホーム（自己紹介 + 最近の記事 + 制作物）
│   │   ├── about.astro          # 自己紹介・スキル・経歴
│   │   ├── 404.astro
│   │   └── blog/
│   │       ├── index.astro      # 記事一覧
│   │       └── [slug].astro     # 記事詳細
│   ├── content/
│   │   └── blog/                # ここに .md / .mdx ファイルを追加
│   ├── components/
│   │   ├── Nav.astro
│   │   ├── Footer.astro
│   │   └── Tag.astro
│   ├── layouts/
│   │   └── BaseLayout.astro
│   └── styles/
│       └── global.css
├── public/                      # 静的ファイル（画像など）
└── .github/workflows/
    └── deploy.yml               # GitHub Actions デプロイ設定
```

## 記事の書き方

`src/content/blog/` に Markdown ファイルを追加するだけで記事が公開されます。

```markdown
---
title: "記事タイトル"
description: "記事の説明（一覧・OGPに使用）"
pubDate: 2026-03-29
tags: ["TypeScript", "メモ"]
draft: false   # true にすると下書き（公開されない）
---

## 見出し

本文をここに書く。コードブロックはシンタックスハイライト付きで表示されます。

\`\`\`typescript
const greet = (name: string) => `Hello, ${name}!`;
\`\`\`
```

## カスタマイズ箇所

| ファイル | 変更内容 |
|---------|---------|
| `src/pages/index.astro` | 名前・自己紹介文・使用技術・制作物リスト |
| `src/pages/about.astro` | スキル・経歴・SNSリンク |
| `src/components/Footer.astro` | 名前・SNSリンク |
| `astro.config.mjs` | サイトURL（`site` / `base`） |

## GitHub Pages へのデプロイ

1. GitHub で `my-blog` リポジトリ（Public）を作成
2. `git remote add origin https://github.com/taki395808/my-blog.git`
3. `git push -u origin main`
4. リポジトリの **Settings > Pages > Source** を **"GitHub Actions"** に設定
5. `main` ブランチに push → 自動ビルド・デプロイ

公開URL: `https://taki395808.github.io/my-blog/`
