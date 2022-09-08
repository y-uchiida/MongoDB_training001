# 集計処理(aggregate)内の $split/$concat オペレーター

指定された文字列を分割、結合する

## 凡例

```js
// $split operator
db.<collection_name>.aggregate([
	<stage_name>: {
		<field_name>: {$split: [<text>, <delimiter>]}, // return array
	}
]);

// $concat operator
db.<collection_name>.aggregate([
	<stage_name>: {
		<field_name>: {$concat: [<expression>, [, ...] ]}, // return string
	}
]);
```

## 使用例

```js
// 著者の名前を" "(半角スペース) で分割する
db.authors.aggregate([
  {
    $project: {
      _id: 0,
      name: { $split: ["$name", " "] },
    },
  },
]);
```

```js
// 書籍の「タイトル」を「タイトル（出版社）」の表記で取得
db.books.aggregate([
  {
    $project: {
      _id: 0,
      title: { $concat: ["$title", "(", "$publisher", ")"] },
    },
  },
]);
```
