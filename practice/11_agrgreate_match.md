# 集計処理(aggregate)内の match ステージ

## 凡例

```js
db.<collection_name>.aggregate([
	{$match: {<field_name>: <value>}},
]);
```

```js
// 出版社が「講話社」の本を取得する
db.books.aggregate([{ $match: { publisher: "講話社" } }]);
```

```js
// 出版社が「講話社」以外の本を取得する
db.books.aggregate([{ $match: { publisher: { $ne: "講話社" } } }]);
```

```js
// 価格が500より高い本を取得する
db.books.aggregate([{ $match: { price: { $gt: 500 } } }]);
```

```js
// 価格が500より安い本を取得する
db.books.aggregate([{ $match: { price: { $lt: 500 } } }]);
```

```js
// 出版社の名称に「社」を含む本を取得する
db.books.aggregate([{ $match: { publisher: /社/g } }]);
```

```js
// $and オペレータで、複数の条件式を評価させる場合
db.books.aggregate([
  {
    $match: {
      $and: [{ publisher: "講話社" }, { price: { $gt: 500 } }],
    },
  },
]);

// 補足: 複数の match ステージを書くこともできる
db.books.aggregate([
  { $match: { publisher: "講話社" } },
  { $match: { price: { $gt: 500 } } },
]);
```
