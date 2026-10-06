# 3. セキュリティ

派手な独自実装ではなく、**Rails 標準の仕組みを漏れなく使う** 方針。

## 3-1. 見られるデータを最初から絞る（最重要）

```ruby
# app/controllers/concerns/card_scoped.rb
def set_card
  @card = Current.user.accessible_cards.find_by!(number: params[:card_id])
end
```

- [app/controllers/concerns/card_scoped.rb](../../app/controllers/concerns/card_scoped.rb)

| 書き方 | 他人のカード番号を URL に入れた場合 |
|--------|----------------------------------|
| `Card.find(params[:id])` + 後で権限チェック | チェックを書き忘れると **見えてしまう** |
| `Current.user.accessible_cards.find_by!(...)` | そもそも検索対象に入らず **404** |

**学び**
- 権限チェックを「後から足す」のではなく「検索の時点で絞る」ことで、書き忘れが構造的に起きない
- `CardScoped` を `include` するだけで全コントローラーに同じルールが適用される

## 3-2. マルチテナント（アカウント分離）

- URL の先頭にアカウント ID が入る（`/{account_id}/boards/...`）
- ミドルウェアが `Current.account` を設定し、データはアカウント単位でスコープされる
- [app/models/current.rb](../../app/models/current.rb) / [AGENTS.md](../../AGENTS.md)

## 3-3. 認証・認可

| 対策 | 内容 | コード |
|------|------|--------|
| アカウントへのアクセス制御 | 無効なアカウント・ユーザーを弾く | [concerns/authorization.rb](../../app/controllers/concerns/authorization.rb) |
| ログイン試行の回数制限 | 3分間に10回まで | [sessions_controller.rb](../../app/controllers/sessions_controller.rb)（`rate_limit`） |
| セッションクッキー | 署名付き・`httponly`・`same_site: :lax` | [concerns/authentication.rb](../../app/controllers/concerns/authentication.rb) |
| 期限付きトークン | 配信停止リンクは1か月で失効 | [user/notifiable.rb](../../app/models/user/notifiable.rb)（`generates_token_for`） |
| パスワードレス | マジックリンク・パスキーでログイン | [sessions_controller.rb](../../app/controllers/sessions_controller.rb) |

## 3-4. CI での自動チェック

| ツール | 役割 |
|--------|------|
| [bin/brakeman](../../bin/brakeman) | Rails コードの脆弱性の静的解析 |
| [bin/bundler-audit](../../bin/bundler-audit) | 依存 gem の既知の脆弱性チェック |
| [bin/gitleaks-audit](../../bin/gitleaks-audit) | API キーなどの秘密情報のコミット検出 |

## 自分の考え・疑問
- [ ] パスワードを使わないログイン方式のメリット・デメリットは？
- [ ] （自分の言葉で追記する）
