# 実行計画 - GitHub Pages 対応

**作成日**: 2026-03-28

## 1. 目的

`my-portfolio`（Astro製）を GitHub Pages に公開できるよう修正し、
自動デプロイの CI/CD パイプラインを整備する。

## 2. 前提条件・入力ファイル

| 項目 | 値 |
|------|-----|
| GitHubユーザー名 | `taki395808` |
| リポジトリ名 | `my-portfolio` |
| 公開URL | `https://taki395808.github.io/my-portfolio/` |
| フレームワーク | Astro 6.x + Tailwind CSS v4 |
| 現在のデプロイ先 | AWS S3 + CloudFront（未使用） |

## 3. 作業ステップ

### Step 1: `astro.config.mjs` の修正
- `site` を `https://taki395808.github.io` に変更
- `base` を `/my-portfolio` に追加
- GitHub Pages は静的ホスティングなので `output: 'static'` を明示

### Step 2: GitHub Actions ワークフローの書き替え
- `.github/workflows/deploy.yml` を S3 → GitHub Pages 用に書き替え
- `actions/deploy-pages` を使用した標準的な GitHub Pages デプロイフローに変更

### Step 3: ソース内プレースホルダーの更新
- `yourusername` → `taki395808` に置換（Nav, Footer, index.astro, about.astro）
- プロジェクト説明文を GitHub Pages ホスティングに合わせて更新

### Step 4: `.gitignore` の確認・補完
- CLAUDE.md ルールに従い、除外禁止ファイル（実行計画・チェック結果等）が除外されていないか確認

### Step 5: git 初期化とコミット
- `git init` → `.gitignore` 確認 → 初回コミット

## 4. 出力成果物

| ファイル | 変更内容 |
|---------|---------|
| `astro.config.mjs` | site/base 設定を GitHub Pages 用に変更 |
| `.github/workflows/deploy.yml` | GitHub Pages デプロイ用に書き替え |
| `src/pages/index.astro` | yourusername → taki395808、説明文更新 |
| `src/pages/about.astro` | yourusername → taki395808 |
| `src/components/Footer.astro` | yourusername → taki395808 |
| `.gitignore` | 除外禁止ファイルの確認・補完 |
| `実行計画_20260328_GitHubPages対応.md` | 本ファイル |

## 5. リスク・注意事項

- `base` パスを設定すると、内部リンクはすべて `/my-portfolio/...` になるが Astro が自動処理するため問題なし
- GitHub Pages の設定で「Source: GitHub Actions」を選択する必要がある（リポジトリ Settings > Pages）
- `CNAME` カスタムドメインを使う場合は別途設定が必要（今回はデフォルトURLを使用）
