# ジャーナリスト向けポートフォリオ

記事、現地写真、取材姿勢、プロフィールを公開するWebサイト。
Astroの静的生成、microCMS、Cloudflare Pagesを採用する想定です。

Astroのローカル開発環境と静的ビルドを用意しています。現在の画面は提案用の比較ページとトップページのラフ3案です。記事詳細ページ・microCMS接続・クライアント向け本番環境の設定は未実施です。

## ローカル起動

Node.js 26.13.0、pnpm 11.6.0を使用します。miseを使う場合は `mise trust` と `mise install` で、このリポジトリの `mise.toml` を有効にします。miseを使わない場合も同じバージョンを用意してください。

```sh
pnpm install --frozen-lockfile
pnpm dev
```

表示先は `http://localhost:4321` です。CMSの認証情報がなくても起動できます。
`.env.example` は将来の接続用テンプレートです。CMS接続を実装する際に `.env` へコピーし、値を設定します。現時点でCMS通信は発生しません。

```sh
pnpm lint       # JS・TS・JSON・CSS等の検査
pnpm typecheck  # Astroを含む型検査
pnpm build      # 型検査後にdist/へ静的生成
pnpm preview    # 生成結果をローカル確認
```

BiomeではAstroファイルの検査を行わず、`astro check` で型検査します。自動テストはまだ導入していません。
Cloudflare Pagesはビルドコマンド `pnpm build`、出力先 `dist`、Node.js 26.13.0を設定する想定です。提案ページには `noindex` を付けています。本番サイト実装時にSEO設定を整えます。

## 提案環境へのデプロイ

公開先と更新方法は[提案用サンプルの公開環境](docs/proposals.md)を参照してください。ダークなルポ調、全面写真の表紙型、サイドバー付きアーカイブ型の3案を比較できます。`pnpm deploy:proposals` で提案専用のPagesを更新します。

## ドキュメント

| ファイル | 内容 |
| --- | --- |
| [アーキテクチャ](docs/architecture.md) | 目的、構成、ページ、デザイン方針、未決事項 |
| [microCMS設計](docs/microcms.md) | 4 APIのフィールドと編集ルール |
| [運用・公開準備](docs/operations.md) | ビルド、Webhook、環境変数、公開前の確認 |
| [納品・本番公開手順](docs/handover.md) | 制作者環境からクライアント環境への公開・引き継ぎ |
| [作業一覧](tasks/todo.md) | 現在の作業と実装前に決めること |
| [教訓](tasks/lessons.md) | 作業の修正と再発防止策 |

## リポジトリ構成

- `docs/`: プロジェクトの設計・運用資料
- `tasks/`: 作業記録
- `.github/`: Issue・PRテンプレート、リポジトリ運用設定
- `biome.jsonc`: Biomeの検査・整形設定

`src/` に起動確認ページ、ルートにAstro・TypeScript・miseの設定を配置しています。依存関係は `pnpm-lock.yaml` で固定します。
GitHub ActionsのCIは依存導入・lint・型検査・ビルドを行う構成です。実際のActions実行はpush後に確認します。Dependabotの自動マージは停止状態を維持しています。
