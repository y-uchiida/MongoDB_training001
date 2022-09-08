# 集計処理(aggregate)内の $lookup ステージ

2 つ以上のコレクションのドキュメントを結合して、ひとつのドキュメントを作る

## 凡例

### 2 つのテーブルを結合する $lookup ステージの記述方法

```js
// $lookup stage 1
db.<src_collection>.aggregate([
	{
		$lookup: {
			from: <trigger_collection>, // 結合するコレクション名
			localField: <src_field_name>, // src_collection(結合される) 側のフィールド名
			foreignField: <trigger_field_name>, // trigger_field(結合する) 側のフィールド名
			as: <out_field_name> // 書き出すフィールド名
		}
	}
]);
```

### 処理内部で aggregation(サブクエリ) を使う場合の $lookup ステージの記述方法

```js
// $lookup stage 2
db.<src_collection>.aggregate([
	{
		$lookup: {
			from: <trigger_collection>, // 結合するコレクション名
			let:  {<var>: <src_field1> [, ...]},
			pipeline: [<expression> [, ...]], // pipelineの中で、さらにaggregation パイプラインを記述できる
			as: <out_field_name> // 書き出すフィールド名
		}
	}
]);
```

- `pipeline` はアグリゲーションパイプラインを記述する
- `pipeline` の軸は from で指定したコレクションになる
- `let` で指定した変数は、 `$expr` を利用して `$$<var>` で呼び出すことができる

## 使用例

```js
// 書籍「D坂の殺人事件」（ ObjectId("5b6f03376c25c37f77e69177") ）へのレビュー(reviews コレクション)の内容を結合して取得

// 1. localField - foreignField を使った例
db.books.aggregate([
  {
    $match: {
      _id: ObjectId("5b6f03376c25c37f77e69177"),
    },
  },
  {
    $lookup: {
      from: "reviews", // 結合するコレクション名
      localField: "_id", // books._id
      foreignField: "book", // reviews._book
      as: "reviews", // 結合した結果を入れるフィールド名
    },
  },
]);

// 2. pipeline を使った例
db.books.aggregate([
  {
    $match: {
      _id: ObjectId("5b6f03376c25c37f77e69177"),
    },
  },
  {
    $lookup: {
      from: "reviews", // 結合するコレクション名
      let: { targets: "$reviews" }, // books.reviews (books コレクションは、 reviews フィールドにreviews のObjectIdの配列を持っている)
      // pipeline で、reviews コレクションの内容を絞り込む
      pipeline: [
        {
          $match: {
            $expr: {
              $in: ["$_id", "$$targets"], // books.reviews の配列に含まれるId に一致するreviews のドキュメントを取得
            },
          },
        },
      ],
      as: "reviews", // 結合した結果を入れるフィールド名
    },
  },
]);
```
