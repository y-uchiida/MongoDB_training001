# ドキュメントの操作

ドキュメント = RDB でいうところのレコード

1. 指定のコレクションに、 1 件のレコードを追加する  
   `db.<collection_name>.insert({<document_object>})`

```js
db.users.insert({
  email: "sachiko.sato@sample.co.jp",
  password: "P@ssw0rd",
  name: "佐藤 幸子",
});
```

2. 指定のコレクションに存在するドキュメントを一覧表示する  
   `db.<collection_name>.find(<condition>)`  
   引数に条件を記述すると、マッチしたドキュメントを絞り込んで表示できる

```js
db.users.find();
```

3. ドキュメントを更新する  
   `db.<collection_name>.update(<condition>, {$set: <update>} [, multi: <bool>])`  
   第 1 引数の更新対象のドキュメントの条件は、JSON 形式で記述する  
   第 3 引数では、条件にマッチしたドキュメントが複数あった場合に、すべてを更新するかを真偽値で設定する

```js
db.users.update(
  {
    email: "sachiko.sato@sample.co.jp",
  },
  {
    $set: {
      password: "qwerty",
    },
  }
);
```

4. ドキュメントを削除する  
   `db.<collection_name>.remove(<condition>)`

```js
db.users.remove({
  email: "sachiko.sato@sample.co.jp",
});
```

5. ドキュメントの件数を取得  
   `db.<collection_name>.count(<condition>)`  
   条件を指定しなければ、指定のコレクションの含まれるドキュメントの全件数を取得する

```js
db.books.count();

db.books.count({ publisher: "新朝社" });
```

6. 検索結果のソート  
   `db.<collection_name>.find(<condition>).sort(<field_name>: <order>)`  
    `order` は asc = 1, desc = -1 で指定する

```js
//価格の安い順で並べ替え
db.books.find().sort({ price: 1 });

//「新朝社」が出版した書籍を、出版日の新しい順で並べ替え
db.books.find({ publisher: "新朝社" }).sort({ launch: -1 });
```

6. 検索結果の取得件数の制限

   1. 指定件数だけ取得
      `db.<collection_name>.find(<condition>).limit(<number>)`

   ```
   出版日の新しい書籍を1件だけ取得
   db.books.find().sort({launch: -1}).limit(1)
   ```

   7. 先頭から指定件数を除外
      `db.<collection_name>.find(<condition>).skip(<number>)`

   ```
   出版日が2番目に新しい書籍1件を取得(先頭1件を除外)
   db.books.find().sort({launch: -1}).skip(1).limit(1)
   ```
