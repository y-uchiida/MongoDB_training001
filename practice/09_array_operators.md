# 配列演算子

MongoDB は JSON を格納できるため、ドキュメント内に配列データを持つことができる  
ドキュメント内の配列を対象にした演算子が用意されている

1. 配列の要素内容の判定 $elemMatch
   指定のフィールドの値の配列内に、条件に一致する要素が含まれているかを判定する

```js
// カテゴリに「日本文学」を含む本を取得する
db.books.find({
  categories: {
    $elemMatch: { $eq: "日本文学" },
  },
});

// 文字列を比較する場合、elemMatch の表記を省略することができる
db.books.find({
  categories: "日本文学",
});

// $ne などの場合も省略可能
db.books.find({
  categories: { $ne: "日本文学" },
});
```

```js
// 「2018年2月1日」以降に本を読み終わっているユーザーを取得する
db.users.find({
  bookshelf: {
    $elemMatch: {
      status: "読了",
      readed: { $gte: ISODate("2018-02-18T00:00:00+09:00") },
    },
  },
});

// オブジェクト配列の場合、"<配列名>.<要素オブジェクトのプロパティ名>" と指定すると、その要素に対する条件を記載できる
db.users.find({
  "bookshelf.readed": {
    $gte: ISODate("2018-02-18T00:00:00+09:00"),
  },
});
```

2. 配列の要素数を取得する $size

```js
// カテゴリ登録数が3つの本を取得する
db.books.find({
  categories: { $size: 3 },
});
```

```js
// カテゴリが１件以上登録されている本を取得する
// 要素演算子 $size では、要素数を大小比較できない
// その場合はアグリゲータを使う
db.books.find({
  $expr: {
    $gt: [{ $size: "$categories" }, 1],
  },
});
```
