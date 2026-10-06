# 7. 開発環境と構成

## サーバーは 1 つだけ

| 構成 | 起動するもの |
|------|------------|
| React + API（よくある構成） | フロント用サーバー（Vite など）＋ バックエンド API の 2 つ |
| Fizzy（Rails + Hotwire） | Rails サーバー 1 つ（HTML 生成・JS 配信・DB・ログインすべて担当） |

1 つで済む理由：
1. 画面（HTML）はサーバー側の ERB で生成する
2. `package.json` がなく、importmap で JS をビルドせずに配信する（[config/importmap.rb](../../config/importmap.rb)）
3. 開発環境ではバックグラウンドジョブも同じプロセス内で動く（[config/puma.rb](../../config/puma.rb)）

起動定義の [Procfile.dev](../../Procfile.dev) も 1 行のみ。

## 開発で Docker を使わない

| 場面 | Docker | 使うもの |
|------|--------|---------|
| ローカル開発 | 使わない | `bin/setup` → `bin/dev` |
| 本番デプロイ | 使う | Kamal が [Dockerfile](../../Dockerfile) からイメージを作成 |
| セルフホスト | 使う | 公式イメージ（[docker-deployment.md](../docker-deployment.md)） |

Docker の代わりに、バージョンをファイルで固定してツールでそろえている：

| そろえるもの | 仕組み | ファイル |
|------------|--------|---------|
| Ruby | mise | [.ruby-version](../../.ruby-version) |
| 開発ツール | mise | [.mise.toml](../../.mise.toml) |
| gem | Bundler | [Gemfile.lock](../../Gemfile.lock) |
| OS ライブラリ | Homebrew | [Brewfile](../../Brewfile) |
| 上記をまとめて導入 | スクリプト | [bin/setup](../../bin/setup) |

Mac（開発）と Linux（本番）の差は、CI（Linux 上で SQLite / MySQL 両方をテスト）で吸収している。

**判断基準として理解したこと**：サービスが多い・OS が混在するなら Docker、構成がシンプルならネイティブ環境の方が速く扱いやすい。

## セットアップで詰まったこと

### 症状
`bin/dev` 実行時に次のエラー。

```
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
bin/dev: line 118: foreman: command not found
```

### 原因
`bin/setup` で mise と Ruby 3.4.8 はインストールされていたが、シェルで mise が有効になっておらず、
macOS に標準で入っている **Ruby 2.6** が使われていた（エラー中の `2.6.0` が手がかり）。

### 解決

```sh
echo 'eval "$(mise activate zsh)"' >> ~/.zshrc
source ~/.zshrc
ruby -v   # ruby 3.4.8 を確認
bin/dev
```

**学び**：`sudo gem install` で無理やり入れるのではなく、エラーメッセージのパスから「どの Ruby が使われているか」を特定して根本原因を直す。

## 起動手順（まとめ）

```sh
bin/setup   # 初回のみ
bin/dev     # http://app.fizzy.localhost:3006
```

ログインは `david@example.com`。確認コードは開発環境では画面に表示される。
