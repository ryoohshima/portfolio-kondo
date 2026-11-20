# 提案用サンプルの公開環境

## 構成

制作者のCloudflareアカウントに、提案専用のPagesプロジェクト `portfolio-kondo-proposals` を作成しています。クライアントの本番環境とは別です。

| URL | 内容 |
| --- | --- |
| https://portfolio-kondo-proposals.pages.dev/ | 3案の比較用入口 |
| https://portfolio-kondo-proposals.pages.dev/sample-1/ | サンプル1 |
| https://portfolio-kondo-proposals.pages.dev/sample-2/ | サンプル2 |
| https://portfolio-kondo-proposals.pages.dev/sample-3/ | サンプル3 |

トップページのラフ3案を制作しています。1つのサイト内で3案を比較する構成です。記事詳細やCMS接続は未実装です。
CMSを接続せず、静的サンプルとして制作する想定です。実在人物の実績と誤認される原稿・写真は使用しません。

## 3案の方向性

| 案 | 方向性 |
| --- | --- |
| 1 / Field Report | ダークな背景とSaltivkaの実際の写真で、ウクライナ取材を伝える構成 |
| 2 / Cover Story | 全面写真に見出しを重ねた雑誌表紙型。記事一覧は横長の行で配置 |
| 3 / Field Archive | 左に追従するプロフィール、右に写真と記事を配置するアーカイブ型。スマートフォンでは縦に並べる |

各案のナビゲーションはトップページ内を移動します。氏名・プロフィール・記事名・公開日はご本人の公開noteを参照しています。独自の見出し・紹介文は提案コピーです。連絡先は未設定です。

## 更新して公開

Node.js 26.13.0、pnpm 11.6.0を使用します。

```sh
pnpm install --frozen-lockfile
pnpm exec wrangler whoami
pnpm lint
pnpm deploy:proposals
```

`whoami` で制作者のアカウントであることを確認します。未ログインなら `pnpm exec wrangler login` で認証します。認証情報はGitに保存しません。
`pnpm deploy:proposals` は型検査・ビルド後、`wrangler.jsonc` で指定したPagesプロジェクトの `main` 配信先を更新します。公開する変更は先にコミットしてください。

今回はDirect Upload方式を採用しています。pushだけでは自動デプロイしません。ローカルでビルドするため、Cloudflare側のNode.js設定には依存しません。
Direct Uploadのプロジェクトを後からGit連携方式へ変更することはできません。Git連携が必要になった場合は新規プロジェクトを作成します。[Cloudflare公式ドキュメント](https://developers.cloudflare.com/pages/get-started/direct-upload/)

## 制作時の配置

- `src/pages/index.astro`: 比較用入口。
- `src/pages/sample-1/index.astro` から `sample-3/index.astro`: 各案のトップページ。
- `src/layouts/ProposalLayout.astro`: 比較ページのレイアウト。
- `src/layouts/SampleLayout.astro`: 各案の共通ナビゲーション・フッターとテーマ。
- `public/_headers`: 提案サイト全体への `X-Robots-Tag: noindex, nofollow` 指定。
- `wrangler.jsonc`: 提案専用プロジェクト名と出力先。

検索除外を指定していますが、認証による閲覧制限はありません。URLを知っている人は閲覧できます。非公開資料は掲載しません。

## 納品時

採用案を決めた後、CMS接続と本番サイトを実装します。クライアント所有のCloudflareには、[納品手順](handover.md)に従って本番用プロジェクトを別途作成します。
提案用の `wrangler.jsonc` をそのまま使うと制作者の公開先を更新するため、本番用設定とコマンドを分けます。提案用の検索除外設定も、本番公開前に見直します。

## クライアントと素材の出典

クライアントは [RYOSUKE KONDO](https://note.com/london_futa17) さん。ウクライナを定期的に訪れ、戦争と戦時下の暮らしをnoteで発信しています。
氏名の漢字表記や現在の拠点は推測で補わず、公開されている欧文名を使用しています。

| 記事 | 公開日 | ローカル画像 |
| --- | --- | --- |
| [ウクライナルポ #16](https://note.com/london_futa17/n/n373b2d147e23) | 2026-06-11 | `public/images/kharkiv-afternoon.jpg` |
| [ウクライナルポ #15](https://note.com/london_futa17/n/ncc9e20ff8676) | 2026-06-11 | `public/images/saltivka.jpg` |
| [ウクライナルポ #14](https://note.com/london_futa17/n/n4ab31366e988) | 2026-05-29 | `public/images/kharkiv-night.jpg` |

公開noteの見出し画像を提案ラフに使用しています。noteは内容確認・素材参照のための資料で、サイトからの外部誘導先にはしません。本文の転載や写真の生成・加工は行っていません。レイアウト上のトリミングはあります。
記事はmicroCMSで管理し、ポートフォリオ内の `/reports/[id]/` に本文を静的生成する当初の設計を維持します。現在はトップページのみのラフのため、記事カードはリンクなしで表示し、詳細ページは未実装です。
記事情報は `src/data/reports.ts` にまとめています。取得日時点の固定データで、自動同期はしません。
本番公開時には、写真の利用範囲、クレジット、提案コピーをご本人と確認します。
