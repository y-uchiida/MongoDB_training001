# ビューの作成: createView()

アグリゲーションパイプラインを用いて、ビューを作成する  
指定された仮想コレクションみたいなもの

## 凡例

```js
db.createView(<view_name>, <src_collection>, <pipeline>);
```

- view_name: 作成するビューの名称
- src_collection: 軸となるコレクションの名称
- pipeline: ビューの内容となるアグリゲーションパイプライン

## システム変数

アグリゲーションパイプラインの中で利用できる変数

- `$$ROOT` :  
   アグリゲーションパイプラインで現在処理しているトップレベルドキュメントを指す

- `$$CURRENT` :  
   アグリゲーションパイプラインで現在処理しているステージの処理中ドキュメントを指す

## 使用例

```js
// 書籍のカテゴリごとに「件数」および「書籍一覧」を取得
db.books.aggregate([
  { $unwind: "$categories" },
  {
    $group: {
      _id: "$categories",
      count: { $sum: 1 },
      books: { $push: "$$ROOT" }, // $$ROOT で、現在操作している大元のドキュメント (books コレクションのドキュメント)を取得できる
    },
  },
]);
```

```js
// 書籍のカテゴリに「件数」および「書籍一覧」を取得し、categories, count(設定された書籍の件数), books(設定された書籍のドキュメント)のフィールドのみ抽出する
db.books.aggregate([
  { $unwind: "$categories" },
  {
    $group: {
      _id: "$categories",
      count: { $sum: 1 },
      books: { $push: "$$ROOT" }, // $$ROOT で、現在操作している大元のドキュメント (books コレクションのドキュメント)を取得できる
    },
  },
  {
    $project: {
      _id: 0,
      categories: "$_id",
      count: 1,
      books: 1,
    },
  },
]);
```

```js
// 書籍のカテゴリに「件数」および「書籍一覧」を取得し、categories, count(設定された書籍の件数), books(設定された書籍のドキュメント)のフィールドのみ抽出する
db.createView(
  "categories", // 作成するView名称
  "books", // 元になるコレクション名
  [
    // ビューを生成するためのアグリゲーションパイプライン
    { $unwind: "$categories" },
    {
      $group: {
        _id: "$categories",
        count: { $sum: 1 },
        books: { $push: "$$ROOT" },
      },
    },
    {
      $project: {
        _id: 0,
        categories: "$_id",
        count: 1,
        books: 1,
      },
    },
  ]
);
```

```js
// ビューが追加されているかを確認する
show collections
```

```js
// ビューを利用する
// ビューもコレクションと同じように扱うことができる
db.categories.find().pretty();
```
