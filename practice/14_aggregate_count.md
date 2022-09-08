# 集計処理(aggregate)内の count ステージ

返却されるドキュメントデータの件数を取得する

## 凡例

```js
db.<collection_name>.aggregate([
	{$count: "<field_name>"},
]);
```

## 設定の仕方

`$count` に設定するフィールド名は任意のものを指定できる  
既存の項目でもよいし、カウント用に新しい項目にしてもよい

# 使用例

```js
// 登録されている本の件数を取得
db.books.aggregate([{ $count: "count" }]);
```

```js
// 出版社が「新朝社」になっている本の件数を取得
db.books.aggregate([{ $match: { publisher: "新朝社" } }, { $count: "新朝社" }]);
```
