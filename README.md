# Fizzy Code Reading — 実務Railsコードの学習記録

37signals が公開した商用かんばんツール [Fizzy](https://github.com/basecamp/fizzy) を題材に、
**本番で動いているRailsコードの書き方・考え方**を学ぶためのフォークです。

> ⚠️ 本リポジトリは学習目的のフォークです。原著作権は 37signals に帰属し、
> O'Saasy License に従います。SaaSとしての再配布・提供は行いません。

## 📒 学習ノート → [docs/study/](docs/study/README.md)

設計・テスト・セキュリティ・リファクタリングの観点で実務コードを分析し、該当コードへのリンク付きでまとめています。

| # | ノート | 内容 |
|---|--------|------|
| 0 | [アプリの全体像](docs/study/00-app-overview.md) | どんなアプリか、カードのライフサイクル |
| 1 | [設計](docs/study/01-architecture.md) | concern によるモデル分割、CRUD に限定したコントローラー、薄いジョブ |
| 2 | [テスト](docs/study/02-testing.md) | Minitest + fixtures、状態変化を検証する書き方 |
| 3 | [セキュリティ](docs/study/03-security.md) | データスコープの絞り込み、認証・認可、CI での自動チェック |
| 4 | [命名・コメント](docs/study/04-naming-and-comments.md) | コメントを最小限にする命名、「なぜ」を書くコメント |
| 5 | [リファクタリング](docs/study/05-refactoring.md) | git 履歴から設計の変遷を追う |
| 6 | [サービスの性格と求められる品質](docs/study/06-service-quality-axes.md) | 規模・機密性・影響範囲・規制の4軸で整理 |
| 7 | [開発環境と構成](docs/study/07-dev-environment.md) | 1サーバー構成、Docker を使わない開発、セットアップのトラブル解決 |

## 主な学び（抜粋）
- **権限チェックは「後から足す」のではなく「検索の時点で絞る」**：`Current.user.accessible_cards.find_by!` により、書き忘れが構造的に起きない（[詳細](docs/study/03-security.md)）
- **状態はフラグではなく別テーブルで表す**：「閉じた」を `Closure` レコードで表し、「誰が・いつ」が自然に残る（[詳細](docs/study/01-architecture.md)）
- **求められる品質は複数の軸で決まる**：規模・機密性・影響範囲・規制によって手続きや設計は変わるが、基本品質は共通（[詳細](docs/study/06-service-quality-axes.md)）

## 環境
- Ruby 3.4.8 / Rails 8.2.0.alpha（main ブランチ）/ SQLite
- ローカル起動：`bin/setup` → `bin/dev` → http://app.fizzy.localhost:3006 （[手順とトラブル対応](docs/study/07-dev-environment.md)）

---

*以下はフォーク元（basecamp/fizzy）のオリジナル README です。*

# Fizzy

This is the source code of [Fizzy](https://fizzy.do/), the Kanban tracking tool for issues and ideas by [37signals](https://37signals.com).


## Running your own Fizzy instance

The easiest way to self-host Fizzy is with [ONCE](https://github.com/basecamp/once).
It will guide you through the initial setup, and keep your instance up to date automatically. It has everything you need for a fully-functional, single-machine deployment.

To get started, run this on the machine where you want to install Fizzy:

```sh
curl https://get.once.com/fizzy | sh
```

### Deploying with Docker

If you'd rather run our pre-built Docker image yourself, you can find the details in our [Docker deployment guide](docs/docker-deployment.md).

### Deploying with Kamal

If you want more flexibility to customize your Fizzy installation by changing its code, and deploy those changes to your server, then we recommend you deploy Fizzy with Kamal. You can find a complete walkthrough of doing that in our [Kamal deployment guide](docs/kamal-deployment.md).


## Development

You are welcome -- and encouraged -- to modify Fizzy to your liking.
Please see our [Development guide](docs/development.md) for how to get Fizzy set up for local development.


## Contributing

We welcome contributions! Please read our [style guide](STYLE.md) before submitting code.


## License

Fizzy is released under the [O'Saasy License](LICENSE.md).
