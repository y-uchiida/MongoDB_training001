# 集計処理(aggregate)内の $cond/$switch オペレーター

条件に応じて、検索結果の表示内容を切り替える

## 凡例

```js
// $cond operator
db.<collection_name>.aggregate([
	<stage_name>: {
		<field_name>: {
			$cond: {
				if: <expression>,
				then: <true>, // if で記述した条件が真の場合の表示内容
				else: <false> // 条件が偽の場合の表示内容
			}
		}
	}
]);

// $switch operator
db.<collection_name>.aggregate([
	<stage_name>: {
		<field_name>: {
			$switch: {
				branches: [
					{case: <expression>, then: <case>} // 条件を満たした場合に表示される内容
					[, ... ]
				],
				default: <expression>　// いずれのケースにも該当しなかった場合に表示される内容
			}
		}
	}
]);
```

## 使用例

```js
// 書籍のレビュー件数が2件以上ついている場合、「ステータス」に"注目"　を設定し、そうでなければ "" を設定する
db.books.aggregate([
  {
    $project: {
      _id: 0,
      title: 1,
      status: {
        $cond: {
          if: { $gte: [{ $size: "$reviews" }, 2] },
          then: "注目",
          else: "",
        },
      },
    },
  },
]);
```

```js
// 書籍のレビュー件数が2件以上だとステータス "A", 1件は "B", その他は "C" を設定する
db.books.aggregate([
  {
    $project: {
      _id: 0,
      title: 1,
      status: {
        $switch: {
          branches: [
            // 1. reviews が2件以上ならA
            { case: { $gte: [{ $size: "$reviews" }, 2] }, then: "A" },
            // 2. reviews が1件ならB
            { case: { $eq: [{ $size: "$reviews" }, 1] }, then: "B" },
          ],
          default: "C",
        },
      },
    },
  },
]);
```
