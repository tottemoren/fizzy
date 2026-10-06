# 1. 設計

Fizzy は STYLE.md で「[vanilla Rails](https://dev.37signals.com/vanilla-rails-is-plenty/)」を掲げており、
**薄いコントローラー＋リッチなドメインモデル** で構成されている。Service クラスは基本的に使わない。

## 1-1. モデルは concern の組み合わせで作る

中心となる `Card` モデルの本体は短く、20 以上の concern を `include` している。

```ruby
# app/models/card.rb
class Card < ApplicationRecord
  include Accessible, Assignable, Attachments, Broadcastable, Closeable, Colored, Commentable,
    Entropic, Eventable, Exportable, Golden, Mentions, Multistep, Pinnable, Postponable, ...
```

- 本体：[app/models/card.rb](../../app/models/card.rb)
- 部品の一覧：[app/models/card/](../../app/models/card/)

### 例：`Card::Closeable`（[closeable.rb](../../app/models/card/closeable.rb)）

```ruby
def close(user: Current.user)
  unless closed?
    transaction do
      not_now&.destroy
      create_closure! user: user
      track_event :closed, creator: user
    end
  end
end
```

**学び**
- 「閉じた」状態を `closed: true` のフラグではなく **`Closure` という別テーブル** で表している
  → 「誰が・いつ閉じたか」が自然に残り、`closed_by` / `closed_at` がそのまま書ける
- 状態変更・関連データの削除・イベント記録を `transaction` でまとめ、途中で失敗しても不整合が残らない
- 1ファイル 50 行程度に収まり、機能単位で読める

## 1-2. コントローラーは CRUD だけ

独自アクション（`post :close`）を足さず、操作を **リソース** として表す。

```ruby
# config/routes.rb
resources :cards do
  resource :closure   # 閉じる = Closure を create / 再オープン = destroy
  resource :goldness
end
```

| 比較 | ルーティング |
|------|-------------|
| よくある書き方 | `post :close` / `post :reopen` |
| Fizzy | `resource :closure` → `create` / `destroy` |

- ルート：[config/routes.rb](../../config/routes.rb)（`scope module: :cards` 付近）
- コントローラー：[cards/closures_controller.rb](../../app/controllers/cards/closures_controller.rb)
- 一覧：[app/controllers/cards/](../../app/controllers/cards/) — ファイル名だけでカードに可能な操作がわかる

**学び**
- コントローラーの中身は `@card.close` を呼ぶだけで、ロジックはモデル側にある
- アクションが増えないので、各コントローラーが小さく保たれる

## 1-3. 非同期ジョブは薄く書く

ジョブはモデルのメソッドを呼ぶだけ。非同期版のメソッドには `_later` を付ける。

```ruby
# app/jobs/notify_recipients_job.rb
class NotifyRecipientsJob < ApplicationJob
  def perform(notifiable)
    notifiable.notify_recipients
  end
end
```

- ジョブ：[notify_recipients_job.rb](../../app/jobs/notify_recipients_job.rb)
- モデル側：[concerns/notifiable.rb](../../app/models/concerns/notifiable.rb)（`notify_recipients` と `notify_recipients_later` のペア）

**学び**
- ロジックがモデルにあるので、同期でも非同期でも同じコードをテストできる

## 1-4. フロントエンド（Hotwire）

- 画面はサーバー側で HTML を生成（ERB）
- 操作後は Turbo Stream で画面の一部だけを差し替える：[cards/closures/create.turbo_stream.erb](../../app/views/cards/closures/create.turbo_stream.erb)
- JS は小さな Stimulus コントローラー（約60個）：[app/javascript/controllers/](../../app/javascript/controllers/)
- npm・ビルドなし。importmap で JS をそのまま配信：[config/importmap.rb](../../config/importmap.rb)

## 一般的な書き方との比較

| 観点 | Fizzy | 一般的な現場でよく見る形 |
|------|-------|------------------------|
| ロジックの置き場所 | モデル（concern） | Service クラス |
| 独自操作 | 別リソースとして CRUD | コントローラーに独自アクション |
| フロント | Hotwire（サーバーで HTML 生成） | React などの SPA + API |

## 自分の考え・疑問
- [ ] concern が増えすぎると、どのメソッドがどこにあるか追いにくくならないか？
- [ ] （自分の言葉で追記する）
