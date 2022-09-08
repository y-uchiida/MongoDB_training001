# 集計処理(aggregate)内の $skip/$limit ステージ

返却されるドキュメントデータの取得開始位置と最大件数を指定する

## 凡例

```js
// $skip stage
db.<collection_name>.aggregate([
	{ $skip: <number> },
]);

// $limit stage
db.<collection_name>.aggregate([
	{ $limit: <number> },
]);
```

## 使用例

```js
// 書籍の出版日が新しいもの順に「タイトル」と「出版日」を取得
db.books.aggregate([
  { $sort: { launch: -1 } },
  {
    $project: {
      _id: 0,
      title: 1,
      launch: 1,
    },
  },
]);
```

```js
// 出版日が新しいもの順から3つ、タイトルと出版日を取得
db.books.aggregate([
  { $sort: { launch: -1 } },
  {
    $project: {
      _id: 0,
      title: 1,
      launch: 1,
    },
  },
  { $limit: 3 },
]);
```

```js
// 出版日が新しいもの順で3番目から3件のタイトルと出版日を取得
db.books.aggregate([
  { $sort: { launch: -1 } },
  {
    $project: {
      _id: 0,
      title: 1,
      launch: 1,
    },
  },
  { $skip: 2 }, // 先頭の 0 と 1を飛ばして、 2の要素から取得する
  { $limit: 3 },
]);
```
