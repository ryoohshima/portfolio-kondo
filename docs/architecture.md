# アーキテクチャ設計

## 目的と前提

非エンジニアのジャーナリストがmicroCMSで記事や写真を更新し、読者・編集者が実績や取材姿勢を確認できるサイトを作ります。
Astroで静的生成し、Cloudflare Pagesから配信する方針です。実装は未着手です。

## 全体構成

```text
編集者 → microCMSで公開・更新
              ↓ Webhook POST
         Cloudflare PagesのDeploy Hook
              ↓ GitHubのコード取得・CMSデータ取得
         Astroの静的ビルド → dist/ → CDN → 読者
```

CMS APIへのアクセスはビルド時に限定します。APIキーはブラウザに渡しません。
静的配信ではCloudflare用SSRアダプターを導入しません。

## ページ構成

| URL | 内容 |
| --- | --- |
| `/` | ステートメント、メイン写真、記事抜粋、写真抜粋、掲載媒体 |
| `/reports/` | 記事一覧。日付・取材地・掲載媒体・カテゴリを表示 |
| `/reports/[id]/` | 記事詳細。公開中の記事を全件静的生成 |
| `/gallery/` | 写真、撮影地、撮影時期、現場メモ |
| `/about/` | 氏名、肩書き、取材姿勢、略歴、連絡先 |
| `/404.html` | 存在しない記事などの案内 |

検索、問い合わせフォーム、多言語化、下書きプレビューは初期範囲に含めません。必要性が確認できた段階で検討します。

## 実装時の配置案

```text
src/
  layouts/BaseLayout.astro
  components/
    Header.astro
    Footer.astro
    ReportCard.astro
    PhotoFrame.astro
  pages/
    index.astro
    reports/index.astro
    reports/[id].astro
    gallery/index.astro
    about.astro
    404.astro
  lib/microcms.ts
  styles/global.css
astro.config.mjs
.env.example
package.json
```

取得処理は `src/lib/microcms.ts` に集約し、型定義、応答検証、全件取得を扱います。
件数がAPIの取得上限を超えても欠落しないよう、ページ単位で取得します。
取得失敗時はビルドを失敗させ、空データで公開中のサイトを置き換えません。

## ビジュアル方針

- 背景: マットな黒・チャコール（`#0d0e10` から `#141619`）。
- 文字: オフホワイト（`#e6e4df`）、補助情報はミュートグレー（`#9a9892`）。実装時にコントラストを検証します。
- アクセント: くすんだクリムゾン、アンバー、オリーブのうち1色。色は未決定です。
- 見出し・引用・ステートメント: 明朝体と欧文セリフ体。メタ情報: 読みやすいサンセリフ体。
- 写真を主役にし、矩形と余白で構成します。スマートフォン、キーボード操作、動きを減らす設定に対応します。

依頼文のタイポグラフィの記述は途中までのため、フォントと詳細な組版は未確定です。

## 未決事項

- 公開名、英語表記、実際のプロフィール、記事、写真とその利用権。
- メインビジュアル、アクセント色、フォント。
- 外部掲載記事へのリンクが必要か。必要なら `reports` にURLフィールドを追加。
- 公開する連絡先とSignal・PGP案内の範囲。
- ドメイン、GitHub連携先、Cloudflare・microCMSの管理者。
- Node.js、Astro、パッケージマネージャーのバージョン。

## 参考

- [Cloudflare PagesのAstroガイド](https://developers.cloudflare.com/pages/framework-guides/deploy-an-astro-site/)
- [microCMSのコンテンツ一覧取得](https://document.microcms.io/content-api/get-list-contents)
