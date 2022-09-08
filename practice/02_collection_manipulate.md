# コレクションの操作

1. データベースに新しいコレクション `test` を作成する

```js
db.createCollection("test");
```

2. 接続中のデータベースに存在しているコレクションの一覧を表示する

```js
show collections
```

3. コレクション名を変更する  
   `db.<collection_name>.renameCollection('<new_name>'[, drop_old_collection])`  
   `renameCollection()` は既存のコレクションを別の名前で複製するメソッド  
    第 2 引数の真偽値を`true` にすると元のコレクションを削除するので、実質的にコレクションの名前変更になる

```js
db.test.renameCollection("new_test", true);
```

4. コレクションの削除  
   `db.<collection_nam>.drop()`

```js
db.test.drop();
```
