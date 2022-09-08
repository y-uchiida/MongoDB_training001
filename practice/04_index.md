# コレクションに対するインデックスの設定

1. インデックスの作成  
   `db.<collection_name>.createIndex({<field_name>: order} [, <option>])`  
   `<option>` では インデックス名を指定できる  
   インデックスの削除時や、検索・絞り込み時にインデックス利用を強制する場合に使うので指定しておいたほうがよい

```js
// users コレクションの email フィールドに、昇順のインデックスを作成する
db.users.createIndex({ email: 1 }, { name: "IX_EMAIL" });
```

2. インデックスの表示
   `db.<collection_name>.getIndexes()`

```js
// users コレクションに作成されているインデックスを一覧表示する
db.users.getIndexes();
```

3. インデックスの削除
   `db.<collection_name>.dropIndex(<index_name>)`

```js
// users コレクションの IX_EMAIL インデックスを削除する
db.users.dropIndex("IX_EMAIL");
```

4. ユニーク制約付きのインデックス
   `createIndex()` の際に、オプションに `unique: true` を指定する

```js
// users コレクションの email フィールドに、ユニーク制約付きの昇順のインデックスを作成する
db.users.createIndex({ email: 1 }, { name: "IX_EMAIL", unique: true });
```
