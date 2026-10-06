# 5. リファクタリング

## git 履歴という教材
- 最初のコミットは 2024-06-21 の「New Rails app」
- コミット数は 1 万を超え、設計の試行錯誤がそのまま残っている

## 履歴の追い方

```sh
# 1ファイルの変更履歴（リネーム前も追う）
git log --oneline --follow -- app/models/card/closeable.rb

# 各行を最後に変更したコミット
git blame app/models/card/closeable.rb

# あるコミットの差分を見る
git show <コミットID>
```

## 発見したこと

### 「Bubble」から「Card」への一斉リネーム
`closeable.rb` の履歴を遡ると、`Rename bubbles => cards` というコミットがある。
開発途中で、中心となるモデルの名前を **まるごと付け替えている**。

**学び**
- 名前は一度決めたら終わりではなく、よりよい名前が見つかれば大規模でも変える
- 命名を重視する文化（[04-naming-and-comments.md](04-naming-and-comments.md)）が履歴にも表れている

### 小さな改善の積み重ね
同じファイルの履歴に `Rename constant` などの小さなコミットが並んでいる。
一度に大きく書き換えるのではなく、小さな単位で改善を続けていることがわかる。

## 次に追ってみたいもの
- [ ] `app/models/card.rb` がどのように concern へ分割されていったか
- [ ] エントロピー（自動整理）の機能がどう作られたか
- [ ] （自分の言葉で追記する）
