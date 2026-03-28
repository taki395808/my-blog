# AWS デプロイ手順

## 前提条件
- AWS CLI インストール済み・設定済み
- GitHub リポジトリに push 済み

---

## Step 1: S3 バケット作成

```bash
# バケット名は全世界でユニークである必要があります
BUCKET_NAME="your-portfolio-bucket-$(date +%s)"
REGION="ap-northeast-1"

aws s3 mb s3://$BUCKET_NAME --region $REGION
echo "Bucket: $BUCKET_NAME"
```

パブリックアクセスをブロック（CloudFront 経由でのみアクセス）:

```bash
aws s3api put-public-access-block \
  --bucket $BUCKET_NAME \
  --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

---

## Step 2: CloudFront ディストリビューション作成

AWS コンソール → CloudFront → Create distribution:

| 設定項目 | 値 |
|---------|---|
| Origin domain | 作成したS3バケット |
| Origin access | Origin access control (OAC) を新規作成 |
| Default root object | `index.html` |
| Price class | Use only North America and Europe (安い) or All |
| Compress objects automatically | Yes |

**エラーページ設定**（SPA対応）:
- Error code: `403` → Response page path: `/404.html` → Response code: `404`
- Error code: `404` → Response page path: `/404.html` → Response code: `404`

OAC 作成後、表示されるS3バケットポリシーをコピーして S3 に設定してください。

---

## Step 3: GitHub Actions 用 IAM ロール作成

OIDC プロバイダー設定:

```bash
aws iam create-open-id-connect-provider \
  --url https://token.actions.githubusercontent.com \
  --client-id-list sts.amazonaws.com \
  --thumbprint-list 6938fd4d98bab03faadb97b34396831e3780aea1
```

IAM ポリシー作成（`deploy-policy.json`）:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:DeleteObject", "s3:GetObject", "s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::YOUR_BUCKET_NAME",
        "arn:aws:s3:::YOUR_BUCKET_NAME/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "cloudfront:CreateInvalidation",
      "Resource": "arn:aws:cloudfront::YOUR_ACCOUNT_ID:distribution/YOUR_DISTRIBUTION_ID"
    }
  ]
}
```

```bash
aws iam create-policy \
  --policy-name portfolio-deploy \
  --policy-document file://deploy-policy.json
```

IAM ロール（`trust-policy.json`、GitHubユーザー名とリポジトリ名を置換）:

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::YOUR_ACCOUNT_ID:oidc-provider/token.actions.githubusercontent.com"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": {
        "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
        "token.actions.githubusercontent.com:sub": "repo:YOUR_GITHUB_USERNAME/YOUR_REPO_NAME:ref:refs/heads/main"
      }
    }
  }]
}
```

```bash
aws iam create-role \
  --role-name portfolio-github-actions \
  --assume-role-policy-document file://trust-policy.json

aws iam attach-role-policy \
  --role-name portfolio-github-actions \
  --policy-arn arn:aws:iam::YOUR_ACCOUNT_ID:policy/portfolio-deploy
```

---

## Step 4: GitHub Secrets 設定

GitHub リポジトリ → Settings → Secrets and variables → Actions:

| Secret 名 | 値 |
|-----------|---|
| `AWS_ROLE_ARN` | `arn:aws:iam::123456789012:role/portfolio-github-actions` |
| `S3_BUCKET_NAME` | バケット名 |
| `CLOUDFRONT_DISTRIBUTION_ID` | CloudFront のディストリビューション ID |

---

## Step 5: デプロイ

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

GitHub Actions が自動実行され → S3 にアップロード → CloudFront キャッシュクリア。

---

## カスタムドメイン設定（オプション）

1. Route 53 または外部レジストラでドメイン取得
2. ACM (us-east-1) で SSL 証明書発行
3. CloudFront に証明書とドメイン設定
4. DNS の CNAME を CloudFront ドメインに向ける

---

## 費用目安（個人ブログレベル）

| サービス | 月額 |
|---------|------|
| S3（12ヶ月無料枠内） | $0 |
| CloudFront（永続無料枠） | $0 |
| Route 53（カスタムドメイン使う場合） | ~$0.50 |
| ACM（SSL証明書） | $0 |
