# Fizzy Code Reading — 実務Railsコードの学習記録

37signals が公開した商用かんばんツール [Fizzy](https://github.com/basecamp/fizzy) を題材に、
**本番で動いているRailsコードの書き方・考え方**を学ぶためのフォークです。

> ⚠️ 本リポジトリは学習目的のフォークです。原著作権は 37signals に帰属し、
> O'Saasy License に従います。SaaSとしての再配布・提供は行いません。

## 目的
- チュートリアルでは学べない「現場のコード」の設計判断を理解する
- 自分のコードに取り入れられるパターンを言語化する

## 学習の観点
| 観点 | 着目ポイント | ノート |
|------|-------------|--------|
| 設計 | concern によるモデル分割 / CRUD に限定したコントローラー | [ ] docs/architecture.md |
| テスト | Minitest + fixtures、テスト層の分け方 | [ ] docs/testing.md |
| セキュリティ | 認証・権限チェック、データスコープの絞り方 | [ ] docs/security.md |
| コメント・命名 | コメントを最小限にする命名と構造 | [ ] docs/naming.md |
| リファクタリング | git履歴から設計の変遷を追跡 | [ ] docs/refactoring.md |

## 進め方
1. `config/routes.rb` から機能の全体像を把握
2. 1機能を「ルート → コントローラー → モデル → ビュー → テスト」で縦に追う
3. `git log` / `git blame` で設計の変遷を確認
4. 学んだことを docs/ に記録し、自分の言葉でまとめる

## 学んだこと（随時更新）
- [ ] 例：〇〇 を Service クラスではなく concern で表現する理由
- [ ] 例：「閉じる」操作を独立リソースにすることで得られるメリット

## 環境
- Ruby 3.4.8 / Rails 8.2.0.alpha（main ブランチ）
- ローカルでの起動手順：[ ] 実際に動かしたら記載（公式手順：[Development guide](docs/development.md)）

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
