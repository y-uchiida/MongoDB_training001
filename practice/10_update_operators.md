# ドキュメント更新のための演算子

`update()` メソッドの中で、第 2 引数として渡す演算子によってドキュメントの内容をどのように変化させるかを決めることができる

1. ドキュメントの更新(または) $set  
   `{ $set {<condition> [, {<option>}] } }`
   オプションには複数更新するかどうか、upsert などが指定できる

```js
// 著者 「与謝野 晶子」の誕生日を「1878年12月07日」のDate型に更新
db.authors.update(
  { name: "与謝野 晶子" },
  { $set: { dob: ISODate("1878-12-07T00:00:00+09:00") } }
);
```

2. フィールドの削除 $unset  
   `{ $unset {<field_name>: '' [, ...] } }`

```js
// 著者「正岡 子規」のdob フィールドを1867/10/14 で更新
db.authors.update(
  { name: "正岡 子規" },
  { $set: { dob: ISODate("1867-10-14T00:00:00+09:00") } }
);

// 著者「正岡 子規」のdob フィールドをnull で更新
db.authors.update({ name: "正岡 子規" }, { $set: { dob: null } });

// 著者「正岡 子規」のdob フィールドを削除
db.authors.update({ name: "正岡 子規" }, { $unset: { dob: "" } });
```

3. フィールド名の変更 $rename  
   `{ $rename: {<field_name>: <new_name> } }`

```js
// users テーブルのsex フィールドの名称を gender に変更する
db.users.update(
  {}, // 全レコードを対象にするため、空オブジェクトを条件に指定
  { $rename: { sex: "gender" } },
  { multi: true }
);
```

4. フィールドの値を現在日時で更新 $currentDate  
   `{$currentDate: {field: <bool>}}` `bool` に`true`を指定すると`date`型になり、 `false` を指定すると`timestamp`型になる `timestamp` は MongoDB 内部向けなので、通常は`true`を指定する

```js
// ユーザー「田中 剛」のpassword フィールドを「hoge」に更新し、同時にupdated フィールドに更新した日時を設定する

db.users.update(
  { name: "田中 剛" },
  {
    $set: { password: "hoge" },
    $currentDate: { updated: true },
  }
);
```

5. フィールドを加算して更新 $inc  
  `$inc: {<field_name>: <amount>}` `<amount>` に変化量を記入する  
    減算したい場合は負の数を設定する

```js
// すべてのレビューのスコアを1点加算する
db.reviews.update({}, { $inc: { score: 1 } }, { multi: true });

// すべてのレビューのスコアを1点減点する
db.reviews.update({}, { $inc: { score: -1 } }, { multi: true });
```

6. フィールドを乗算して更新 $mul
  `$mul: {<field_name>: <amount>}` `<amount>` に変化量を記入する  
    除算したい場合は逆数を設定する

```js
// すべてのレビューのスコアを2倍にする
db.reviews.update({}, { $mul: { score: 2 } }, { multi: true });

// すべてのレビューのスコアを半減する
db.reviews.update({}, { $mul: { score: 1 / 2 } }, { multi: true });
```

7. 値の配列に要素を追加する $push  
   ` $push: { <field_name>: { {<modifier>: <value>} [, ...] } }`  
   `<modifier>` に、要素追加と同時に行いたい操作を指定することができる

   `<modifier>`に指定できるものの例  
   | 修飾子(modifier) | 操作内容 |  
   | --- | --- |
   | each | 複数の要素をまとめて追加する |
   | slice | 配列の要素数を切り詰めする |
   | sort | 対象の配列をソートする |
   | position | 要素を追加する位置を指定する |

```js
// ユーザー「田中 剛」の本棚に本を1件追加する
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        book: "書籍1",
        registered: ISODate(), // ISODate() を引数なしで実行すると、現在の日付時刻(世界標準時)が取得できる
      },
    },
  }
);
```

```js
// ユーザー「田中 剛」の本棚に本を2件追加する
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        $each: [
          {
            book: "書籍2",
            registered: ISODate("2000-01-01T00:00:00+09:00"),
          },
          {
            book: "書籍3",
            registered: ISODate("2000-01-02T00:00:00+09:00"),
          },
        ],
      },
    },
  }
);
```

```js
// ユーザー「田中 剛」の本棚を登録日が新しい順にソートする
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        $each: [], // ソートしたいだけなので、追加要素はなし
        $sort: {
          registered: -1,
        },
      },
    },
  }
);
```

```js
// ユーザー「田中 剛」の本棚を先頭から5件に切り詰めする
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        $each: [], // sliceしたいだけなので、追加要素はなし
        $slice: 5,
      },
    },
  }
);
```

```js
// ユーザー「田中 剛」の本棚の先頭に本を1件追加する
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        book: "書籍4",
        registered: ISODate("2020-01-01T00:00:00+09:00"),
        $position: 0,
      },
    },
  }
);
```

```js
// each と併用する場合は以下のようになる
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        $each: [
          {
            book: "書籍4",
            registered: ISODate("2020-01-01T00:00:00+09:00"),
          },
        ],
        $position: 0,
      },
    },
  }
);
```

```js
// ユーザー「田中 剛」の本棚に本を2件追加し、登録日の新しい順にソートし、上位5件に絞り込みする
db.users.update(
  { name: "田中 剛" },
  {
    $push: {
      bookshelf: {
        $each: [
          {
            book: "書籍5",
            registered: ISODate("2020-01-02T00:00:00+09:00"),
          },
          {
            book: "書籍6",
            registered: ISODate("2020-01-03T00:00:00+09:00"),
          },
        ],
        $sort: { registered: -1 },
        $slice: 5,
      },
    },
  }
);
```

8. 配列要素の更新 $[]

   - 配列の要素すべてに対して、`<operator>` と `<value>` で指定した条件にマッチしたものを更新する  
     `<operator>: <array_name>.$[<elm>]: <value> [, ...]`  
      `<array_name>` で指定した配列

   - 指定の要素だけ更新する場合  
     ` <operator>: <array_name>.$[elm]: <value> [, ...]`  
      `<array_name>` で指定した配列

```js
// ユーザー「田中 剛」の本棚に登録されている「三四郎」のstatus を読了に更新する
// 本「三四郎」のid: 5b6f033a6c25c37f77e6917e

db.users.update(
  { name: "田中 剛" },
  {
    $set: {
      // bookshelf はオブジェクト配列なので、配列要素のオブジェクトのプロパティを指定する
      "bookshelf.$[item].status": "読了",
    },
  },
  {
    arrayFilters: [
      // bookshelf 内の配列要素のオブジェクトのうち、 book の値が指定のObjectIdに一致したものを更新対象にする
      { "item.book": ObjectId("5b6f033a6c25c37f77e6917e") },
    ],
  }
);
```

```js
// ユーザー「田中 剛」の本棚に登録されている「三四郎」のstatus を読了に更新し、readed に現在の日付時刻を設定する
db.users.update(
  { name: "田中 剛" },
  {
    $set: {
      "bookshelf.$[item].status": "読了",
      "bookshelf.$[item].readed": ISODate(),
    },
  },
  {
    arrayFilters: [{ "item.book": ObjectId("5b6f033a6c25c37f77e6917e") }],
  }
);
```

```js
// 補足: 要素の絞り込みをせず、全件に対して追加・アップデートを行う例
// すべてのユーザーの本棚に登録されている本に、"isFavorite" の項目を追加する
db.users.update(
  {},
  {
    $set: {
      "bookshelf.$[].isFavorite": false,
    },
  },
  { multi: true }
);
```

9. 配列の先頭または末尾を削除する $pop  
   `$pop: {<array_name>: <1 | -1>}`

   `1`を指定した場合は末尾から、`-1`を指定した場合は先頭から削除する

```js
// ユーザー「田中 剛」の本棚から、末尾1件を削除する
db.users.update(
  { name: "田中 剛" },
  {
    $pop: { bookshelf: 1 }, // bookshelf 配列の末尾から1件を削除する
  }
);
```

10. 配列から指定の要素を削除する $pull  
    `$pull: {"<array_name>": <expression> [, ...]}`  
    配列から、条件にマッチする要素をすべて削除する

```js
// ユーザー「田中 剛」の本棚から、status が「読了」の本をすべて削除する
db.users.update(
  { name: "田中 剛" },
  { $pull: { bookshelf: { status: "読了" } } }
);
```
