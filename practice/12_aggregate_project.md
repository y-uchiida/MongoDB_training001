# 集計処理(aggregate)内の project ステージ

返却されるドキュメントデータに、フィールドを含めるか否かを設定できる

## 凡例

```js
db.<collection_name>.aggregate([
	{$project: {<field_name>: <1 | 0> [, ...]},
]);
```

## 設定の仕方

設定する項目は、表示するもの(1)か、非表示にするもの(0)かの 2 択  
指定された設定と逆の値が自動設定されるので、1 か 0 どちらかのフィールドだけを記述するのがよい

例えば、email と name だけを受け取りたいなら、 email: 1, name: 1 のみ記述すればよい

ただし、データの固有識別子である `_id` は、自動設定されず原則出力されるので、非表示にする場合は `_id: 0`と明示指定する必要がある

ちなみに 1, 0 は JvaScript ではそれぞれ `true`, `false` に評価されるので、数値ではなく真偽値で条件を書くこともできる

## 使用例

### 表示するフィールドの指定

```js
// books コレクションの_id を非表示にする
db.books.aggregate([
  {
    $project: {
      _id: 0,
    },
  },
]);
```

```js
// books コレクションから、 title のみを取得する
db.books.aggregate([
  {
    $project: {
      _id: 0, // _idの1/0は自動設定されないので、非表示にする場合は個別に記述する
      title: 1,
    },
  },
]);
```

```js
// books コレクションから、_id と title 以外のフィールドを取得する
db.books.aggregate([
  {
    $project: {
      _id: 0,
      title: 0, // title 以外のフィールドは1が設定される
    },
  },
]);
```

### フィールド名を変更する

```js
// 出版社ごとに書籍数を集計
db.books.aggregate([
  {
    $group: {
      _id: "$publisher",
      count: { $sum: 1 },
    },
  },
]);
```

```js
// 出版社ごとに書籍数を集計し、 publisher フィールドに出版社名、 count フィールドに書籍数を表示する
db.books.aggregate([
  {
    $group: {
      _id: "$publisher",
      count: { $sum: 1 },
    },
  },
  {
    $project: {
      _id: 0,
      publisher: "$_id",
      count: 1,
    },
  },
]);
```
