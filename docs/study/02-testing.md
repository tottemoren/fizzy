# 2. テスト

## 構成

| 項目 | Fizzy | よく見る別の選択肢 |
|------|-------|------------------|
| テストフレームワーク | Minitest（Rails 標準） | RSpec |
| テストデータ | fixtures（YAML） | FactoryBot |

ディレクトリは [test/](../../test/) 以下に `models` / `controllers` / `system` / `jobs` などがあり、
**モデルの concern とテストファイルが 1 対 1 で対応** している。

- `app/models/card/closeable.rb` ↔ [test/models/card/closeable_test.rb](../../test/models/card/closeable_test.rb)

## モデルテスト

```ruby
test "close cards" do
  assert_not cards(:logo).closed?

  assert_difference -> { cards(:logo).events.count }, +1 do
    cards(:logo).close(user: users(:kevin))
  end

  assert cards(:logo).closed?
  assert_equal users(:kevin), cards(:logo).closed_by
end
```

**学び**
- `cards(:logo)` `users(:kevin)` のように **名前付きの fixtures** を使うので、テストの意図が一行目から読める
- `assert_difference` で「操作の前後で何が変わったか」を確かめる
- 「Not Now 状態から閉じる」など、状態の組み合わせごとにテストケースを分けている

## コントローラーテスト

```ruby
test "create" do
  card = cards(:logo)

  assert_changes -> { card.reload.closed? }, from: false, to: true do
    post card_closure_path(card), as: :turbo_stream
  end
end
```

- [test/controllers/cards/closures_controller_test.rb](../../test/controllers/cards/closures_controller_test.rb)

**学び**
- `assert_changes ... from: false, to: true` で状態変化を明示的に検証
- Turbo Stream と JSON の **両方のレスポンス形式** をテストしている

## テストデータ（fixtures）

- [test/fixtures/cards.yml](../../test/fixtures/cards.yml)
- ID は UUIDv7。fixtures の ID は実行時に作られるレコードより必ず古くなるよう生成されており、
  `.first` / `.last` の結果がテストで安定する（[AGENTS.md](../../AGENTS.md) より）

## テストの実行

```sh
bin/rails test                                       # 全ユニットテスト
bin/rails test test/models/card/closeable_test.rb    # 1ファイルだけ
bin/ci                                               # CI と同じ一式
```

## 自分の考え・疑問
- [ ] fixtures と FactoryBot、それぞれのメリット・デメリットは？
- [ ] （自分の言葉で追記する）
