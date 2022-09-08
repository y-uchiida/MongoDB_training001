# 集計処理(aggregate)内の $unwind ステージ

配列フィールドの作成、展開を行う

## 凡例

### 配列を平面展開する $unwind ステージ

```js
// $unwind stage
db.<collection_name>.aggregate([
	{
		$unwind: "<array_field_name>"
	}
]);
```

### オブジェクトを指定の配列に追加する $push オペレータ

```js
// $push operator
db.<collection_name>.aggregate([
	{
		$group: {
			_id: <collection>,
			<field_name>: {$push: <expression>},
		}
	}
]);
```

## 使用例

```js
// 書籍「D坂の殺人事件」（ ObjectId("5b6f03376c25c37f77e69177") ）のカテゴリを平面展開する
db.books.aggregate([
  {
    $match: {
      _id: ObjectId("5b6f03376c25c37f77e69177"),
    },
  },
  {
    $unwind: "$categories",
  },
]);
```

```js
// 書籍のカテゴリ別に、カテゴリ設定された書籍の件数を取得する
db.books.aggregate([
  { $unwind: "$categories" },
  {
    $group: {
      _id: "$categories",
      count: { $sum: 1 },
    },
  },
]);
```

```js
// 書籍のカテゴリ別に、カテゴリ設定された書籍の件数および書籍名を一覧で取得する
db.books.aggregate([
  { $unwind: "$categories" },
  {
    $group: {
      _id: "$categories",
      count: { $sum: 1 },
      books: { $push: "$title" }, // categories でグループ化した書籍の一覧から、title フィールドの値を books フィールドの配列に追加していく
    },
  },
]);
```
