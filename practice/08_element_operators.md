# 要素演算子

1. フィールドの存在 $exists

```js
// dob が存在している、authors コレクションのドキュメント
db.authors.find({
  dob: { $exists: true },
});
```

2. 値の型チェック $type

```js
// dob が文字列型で設定されている、authors コレクションのドキュメント
db.authors.find({
  dob: { $type: "string" },
});
```

3. 組み合わせた例

```js
// dob が存在していて、かつ値が日付型以外の、authors コレクションのドキュメント
db.authors.find({
  $and: [{ dob: { $exists: true } }, { dob: { $not: { $type: "date" } } }],
});
```
