# dev.log — 個人ポートフォリオ・学習記録サイト

エンジニアの個人ブログ・ポートフォリオサイトです。
Astro + Tailwind CSS で構築し、AWS S3 + CloudFront でホスティングします。

## 技術スタック

| カテゴリ | 技術 |
|---------|------|
| フレームワーク | [Astro](https://astro.build) v5 |
| スタイリング | [Tailwind CSS](https://tailwindcss.com) v4 |
| コンテンツ | Markdown / MDX |
| ホスティング | AWS S3 + CloudFront |
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
my-portfolio/
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
├── .github/workflows/
│   └── deploy.yml               # GitHub Actions デプロイ設定
└── AWS_DEPLOY.md                # AWS セットアップ手順書
```

## 記事の書き方

`src/content/blog/` に Markdown ファイルを追加するだけで記事が公開されます。

```markdown
---
title: "記事タイトル"
description: "記事の説明（一覧・OGPに使用）"
pubDate: 2026-03-24
tags: ["TypeScript", "AWS", "メモ"]
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
| `astro.config.mjs` | サイトURL（デプロイ後に更新） |

## AWSへのデプロイ

詳細な手順は [AWS_DEPLOY.md](./AWS_DEPLOY.md) を参照してください。

概要:
1. S3 バケット作成
2. CloudFront ディストリビューション作成（OAC設定）
3. GitHub Actions 用 IAM ロール作成（OIDC）
4. GitHub Secrets に `AWS_ROLE_ARN` / `S3_BUCKET_NAME` / `CLOUDFRONT_DISTRIBUTION_ID` を設定
5. `main` ブランチに push → 自動デプロイ

## 費用目安

| サービス | 月額 |
|---------|------|
| S3（12ヶ月無料枠内） | 無料 |
| CloudFront（永続無料枠） | 無料 |
| GitHub Actions（Public repo） | 無料 |
| カスタムドメイン（Route 53） | 約 ¥80/月 |
