# 集計処理(aggregate)内の group ステージ

条件によって集計した結果の件数を表示する

## 凡例

```js
db.<collection_name>.aggregate([
	{$group: {
		_id: <condition>,
		<field_name>: { <operator>: <expression> },
		[...]
	}},
]);
```

## 利用できる operator の例

| operator   | 集計操作   |
| ---------- | ---------- |
| $sum       | 合計       |
| $avg       | 平均       |
| $min       | 最小値     |
| $max       | 最大値     |
| $push      | 配列に追加 |
| $stdDevPop | 母標準偏差 |

## 使用例

### 合計の集計: $sum

```js
// 書籍に登録されている出版社(重複排除)
db.books.aggregate([
  {
    $group: {
      _id: "$publisher", // id フィールドに出版社名を表示
    },
  },
]);
```

```js
// 出版社別に登録されている書籍件数
db.books.aggregate([
  {
    $group: {
      _id: "$publisher", // id フィールドに出版社名を表示
      count: { $sum: 1 }, // count フィールドに、_id にグループ分けされたドキュメントの件数を表示
    },
  },
]);
```

```js
// 登録されている書籍件数が多い順に出版社を取得
db.books.aggregate([
  {
    $group: {
      _id: "$publisher", // id フィールドに出版社名を表示
      count: { $sum: 1 }, // count フィールドに、_id にグループ分けされたドキュメントの件数を表示
    },
  },
  { $sort: { count: -1 } }, // $group ステージの集計終了後のデータに対して、 count フィールドの件数順に並べ替え
]);
```

### 合計の集計: $add, $subtract, $multiply, $divide

```js
// 出版社ごとに「価格」と「ページ数」の合計を算出
db.books.aggregate([
  {
    $group: {
      _id: "$publisher", // id フィールドに出版社名を表示
      cost: { $sum: "$price" }, // cost フィールドに、価格の合計値を表示
      page: { $sum: "$pages" }, // page フィールドに、ページ数の合計を表示
    },
  },
]);
```

```js
// 出版社ごとに「100ページあたりの価格」を表示
db.books.aggregate([
  {
    $group: {
      _id: "$publisher", // id フィールドに出版社名を表示
      cost: { $sum: "$price" }, // cost フィールドに、価格の合計値を表示
      page: { $sum: "$pages" }, // page フィールドに、ページ数の合計を表示
    },
  },
  // $group ステージの集計終了後のデータに対して、 $project ステージで取得したいデータを成形する
  {
    // 出版社ごとに(価格合計 / ページ数) * 100 を算出して、cost_per_100pages フィールドに表示
    $project: {
      cost_per_100pages: { $multiply: [{ $divide: ["$cost", "$page"] }, 100] },
    },
  },
]);
```

### 最小値、最大値: $min, $max

```js
// 出版社ごとに、書籍の最高価格、最低価格を算出
db.books.aggregate([
  {
    $group: {
      _id: "$publisher",
      min: { $min: "$price" },
      max: { $max: "$price" },
    },
  },
]);
```

```js
// 出版年月ごとに、書籍の最高価格、最低価格を算出
db.books.aggregate([
  {
    $group: {
      // 出版年月日で集計するため、id用のオブジェクトを定義
      _id: {
        year: { $year: "$launch" },
        month: { $month: "$launch" },
      },
      min: { $min: "$price" },
      max: { $max: "$price" },
    },
  },
]);
```

```js
// 出版年月ごとに、書籍の最高価格、最低価格を算出を算出し、出版年月の新しい順にソートして表示
db.books.aggregate([
  {
    $group: {
      // 出版年月日で集計するため、id用のオブジェクトを定義
      _id: {
        year: { $year: "$launch" },
        month: { $month: "$launch" },
      },
      min: { $min: "$price" },
      max: { $max: "$price" },
    },
  },
  {
    // 出版年月を_id に設定したので、ソートのキーは_idにする
    $sort: { _id: -1 },
  },
]);
```

### 平均、分散: $avg, $stdDevPop

```js
// 書籍全体に対して、「平均価格」と「価格の標準偏差」を算出する
db.books.aggregate([
  {
    $group: {
      _id: 0, // コレクション全体を集計するので、 _id: 0 を指定する
      avg: { $avg: "$price" },
      stdDevPop: { $stdDevPop: "$price" },
    },
  },
]);
```
