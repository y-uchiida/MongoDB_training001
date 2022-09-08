# 評価演算子

1. 正規表現 $regex

```js
// 出版社名に「社」という文字を含む本を取得する
db.books.find({
  publisher: { $regex: /社/g },
});

// $regex は省略して記述することもできる
db.books.find({
  publisher: /社/g,
});
```

2. アグリゲーション $expr
   割愛
