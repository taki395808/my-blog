---
title: "S3 + CloudFront で静的サイトを無料公開する手順"
description: "AWS S3 と CloudFront を使って Astro で作った静的サイトを無料枠でホスティングする方法をまとめました。"
pubDate: 2026-03-24
tags: ["AWS", "S3", "CloudFront", "Astro", "インフラ"]
---

## 構成

```
GitHub → GitHub Actions → S3 → CloudFront → ユーザー
```

## 1. S3 バケット作成

```bash
aws s3 mb s3://your-portfolio-bucket --region ap-northeast-1
```

パブリックアクセス設定（CloudFront 経由のみ許可）:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-portfolio-bucket/*"
    }
  ]
}
```

## 2. CloudFront ディストリビューション作成

- Origin: S3 バケット（OAC 設定）
- デフォルトルートオブジェクト: `index.html`
- エラーページ: 404 → `/404.html`

## 3. GitHub Actions でデプロイ

`.github/workflows/deploy.yml` を参照してください。

## 費用

| サービス | 無料枠 |
|---------|-------|
| S3 | 5GB / 20K GET / 2K PUT（12ヶ月） |
| CloudFront | 1TB / 10M リクエスト（永続） |

個人ブログレベルなら実質無料で運用できます。
