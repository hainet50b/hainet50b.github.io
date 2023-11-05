# SQL Beginner #1（テーブルとカラムの操作）
{:.no_toc}

* toc
{:toc}

## 参考書籍
[SQL 第2版 ゼロからはじめるデータベース操作 \| 翔泳社の本](https://www.shoeisha.co.jp/book/detail/9784798144450){:target="_blank"}

## SQLの3つの原則
- クライアント／サーバ型システムであること
- 2次元表 to 2次元表の関数であること
  - 2次元表 -> 値, () -> 2次元表, () -> 値も取り得る
- ディスクからの取得フェーズとCPUによる計算フェーズがあること

## 二次元表、リスト、値の表現
```sql
-- n行n列の表現は二次元表として扱われる。SQLの最も一般的な表現。
-- FROM句やVALUES句など多くの場所に適用できる。
SELECT 1, 'foo'
UNION SELECT 2, 'bar'
UNION SELECT 3, 'baz';

-- n行1列の表現はリストとして扱われる。IN句などで使用できる。
SELECT 1
UNION SELECT 2
UNION SELECT 3;

-- 1行1列の表現は値として扱われる。あらゆるリテラルに適用できる。
```

## MySQLクライアントのインストール
パッケージマネージャー（Windows）
```powershell
# MySQLサーバーも同時にインストールされる。
scoop install mysql
```

パッケージマネージャー（Mac）
```shell
# MySQLクライアント
brew install mysql-client
```

## MySQLサーバーの起動
```shell
docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=changeme mysql:8.0.34
```

## MySQL Workbenchのインストール
パッケージマネージャー（Windows）
```powershell
scoop install mysql-workbench
```

パッケージマネージャー（Mac）
```shell
brew install mysqlworkbench
```

## テーブルにおけるリストの表現
一つの注文で複数の商品が購入されたことを複数のカラムで表現できる。

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT,
  order_id INT,
  item1 VARCHAR(32),
  item2 VARCHAR(32),
  item3 VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO orders (order_id, item1, item2, item3) VALUES (1, 'protein', 'shaker', 'spoon');

SELECT * FROM orders;
+----+----------+---------+--------+-------+
| id | order_id | item1   | item2  | item3 |
+----+----------+---------+--------+-------+
|  1 |        1 | protein | shaker | spoon |
+----+----------+---------+--------+-------+
```

しかしこれでは任意の個数の商品に対応できないため行で表現する。

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT,
  order_id INT,
  item VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO orders (order_id, item) VALUES (1, 'protein');
INSERT INTO orders (order_id, item) VALUES (1, 'shaker');
INSERT INTO orders (order_id, item) VALUES (1, 'spoon');

SELECT * FROM orders;
+----+----------+---------+
| id | order_id | item    |
+----+----------+---------+
|  1 |        1 | protein |
|  2 |        1 | shaker  |
|  3 |        1 | spoon   |
+----+----------+---------+
```

## データベースの作成
```sql
CREATE DATABASE sql_beginner;
```

## テーブルの作成
```sql
CREATE TABLE items (
  id INT,
  name VARCHAR(100) NOT NULL,
  genre VARCHAR(32) NOT NULL,
  price INT,
  cost INT,
  registered_at DATE,
  PRIMARY KEY (id)
);

INSERT INTO items VALUES (1, 'Tシャツ', '衣服', 1000, 500, '2009-09-20');
INSERT INTO items VALUES (2, '穴あけパンチ', '事務用品', 500, 320, '2009-09-11');
INSERT INTO items VALUES (3, 'カッターシャツ', '衣服', 4000, 2800, NULL);
INSERT INTO items VALUES (4, '包丁', 'キッチン用品', 3000, 2800, '2009-09-20');
INSERT INTO items VALUES (5, '圧力鍋', 'キッチン用品', 6800, 5000, '2009-01-15');
INSERT INTO items VALUES (6, 'フォーク', 'キッチン用品', 500, NULL, '2009-09-20');
INSERT INTO items VALUES (7, 'おろしがね', 'キッチン用品', 880, 790, '2008-04-28');
INSERT INTO items VALUES (8, 'ボールペン', '事務用品', 100, NULL, '2009-11-11');
```

## 型の種類
MySQL：[第 11 章 データ型](https://dev.mysql.com/doc/refman/8.0/ja/data-types.html){:target="_blank"}

## テーブルの削除
```sql
DROP TABLE items;
```

## カラムの追加
カラムを追加すると追加したカラムは結果セットの最後に表示される。  
この物理的な順番を変更することは難しく、必要であれば物理的な順番に頼らず論理的に順番を指定する。

```sql
ALTER TABLE items ADD COLUMN kana VARCHAR(100);

SELECT * FROM items;
+----+-----------------------+--------------------+-------+------+---------------+------+
| id | name                  | genre              | price | cost | registered_at | kana |
+----+-----------------------+--------------------+-------+------+---------------+------+
|  1 | Tシャツ               | 衣服               |  1000 |  500 | 2009-09-20    | NULL |
|  2 | 穴あけパンチ          | 事務用品           |   500 |  320 | 2009-09-11    | NULL |
|  3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          | NULL |
|  4 | 包丁                  | キッチン用品       |  3000 | 2800 | 2009-09-20    | NULL |
|  5 | 圧力鍋                | キッチン用品       |  6800 | 5000 | 2009-01-15    | NULL |
|  6 | フォーク              | キッチン用品       |   500 | NULL | 2009-09-20    | NULL |
|  7 | おろしがね            | キッチン用品       |   880 |  790 | 2008-04-28    | NULL |
|  8 | ボールペン            | 事務用品           |   100 | NULL | 2009-11-11    | NULL |
+----+-----------------------+--------------------+-------+------+---------------+------+

SELECT id, name, kana, genre, price, cost, registered_at FROM items;
+----+-----------------------+------+--------------------+-------+------+---------------+
| id | name                  | kana | genre              | price | cost | registered_at |
+----+-----------------------+------+--------------------+-------+------+---------------+
|  1 | Tシャツ               | NULL | 衣服               |  1000 |  500 | 2009-09-20    |
|  2 | 穴あけパンチ          | NULL | 事務用品           |   500 |  320 | 2009-09-11    |
|  3 | カッターシャツ        | NULL | 衣服               |  4000 | 2800 | NULL          |
|  4 | 包丁                  | NULL | キッチン用品       |  3000 | 2800 | 2009-09-20    |
|  5 | 圧力鍋                | NULL | キッチン用品       |  6800 | 5000 | 2009-01-15    |
|  6 | フォーク              | NULL | キッチン用品       |   500 | NULL | 2009-09-20    |
|  7 | おろしがね            | NULL | キッチン用品       |   880 |  790 | 2008-04-28    |
|  8 | ボールペン            | NULL | 事務用品           |   100 | NULL | 2009-11-11    |
+----+-----------------------+------+--------------------+-------+------+---------------+
```

NOT NULL制約のカラムを追加すると既存のレコードの該当カラムには初期値として空文字が設定される。

```sql
ALTER TABLE items ADD COLUMN foo VARCHAR(8) NOT NULL;

SELECT * FROM items WHERE id = 1;
+----+------------+--------+-------+------+---------------+------+-----+
| id | name       | genre  | price | cost | registered_at | kana | foo |
+----+------------+--------+-------+------+---------------+------+-----+
|  1 | Tシャツ    | 衣服   |  1000 |  500 | 2009-09-20    | NULL |     |
+----+------------+--------+-------+------+---------------+------+-----+

SELECT (SELECT foo FROM items WHERE id = 1) = '1';
+--------------------------------------------+
| (SELECT foo FROM items WHERE id = 1) = '1' |
+--------------------------------------------+
|                                          0 |
+--------------------------------------------+
```

## カラムの削除
```sql
ALTER TABLE items DROP COLUMN kana;
```

## レコードの追加
```sql
INSERT INTO items VALUES (9, 'プロテイン', '食品', 7500, 3750, '2023-10-14', 'ぷろていん');
```

## レコードの確認
```sql
SELECT * FROM items WHERE id = 9;
+----+-----------------+--------+-------+------+---------------+-----------------+
| id | name            | genre  | price | cost | registered_at | kana            |
+----+-----------------+--------+-------+------+---------------+-----------------+
|  9 | プロテイン      | 食品   |  7500 | 3750 | 2023-10-14    | ぷろていん      |
+----+-----------------+--------+-------+------+---------------+-----------------+
```

## レコードの削除
SELECT文で対象を特定してから同様のWHERE句を指定して実行すること。

```sql
DELETE FROM items WHERE id = 9;

SELECT * FROM items WHERE id = 9;
Empty set (0.00 sec)
```

## トランザクション

### コミット
```sql
START TRANSACTION;

INSERT INTO items VALUES (9, 'プロテイン', '食品', 7500, 3750, '2023-10-14', 'ぷろていん');
SELECT * FROM items WHERE id = 9;
+----+-----------------+--------+-------+------+---------------+-----------------+
| id | name            | genre  | price | cost | registered_at | kana            |
+----+-----------------+--------+-------+------+---------------+-----------------+
|  9 | プロテイン      | 食品   |  7500 | 3750 | 2023-10-14    | ぷろていん      |
+----+-----------------+--------+-------+------+---------------+-----------------+

COMMIT;

SELECT * FROM items WHERE id = 9;
+----+-----------------+--------+-------+------+---------------+-----------------+
| id | name            | genre  | price | cost | registered_at | kana            |
+----+-----------------+--------+-------+------+---------------+-----------------+
|  9 | プロテイン      | 食品   |  7500 | 3750 | 2023-10-14    | ぷろていん      |
+----+-----------------+--------+-------+------+---------------+-----------------+
```

### ロールバック
```sql
START TRANSACTION;

INSERT INTO items VALUES (9, 'プロテイン', '食品', 7500, 3750, '2023-10-14', 'ぷろていん');
SELECT * FROM items WHERE id = 9;
+----+-----------------+--------+-------+------+---------------+-----------------+
| id | name            | genre  | price | cost | registered_at | kana            |
+----+-----------------+--------+-------+------+---------------+-----------------+
|  9 | プロテイン      | 食品   |  7500 | 3750 | 2023-10-14    | ぷろていん      |
+----+-----------------+--------+-------+------+---------------+-----------------+

ROLLBACK;

SELECT * FROM items WHERE id = 9;
Empty set (0.00 sec)
```

## テーブル名の変更
```sql
RENAME TABLE items TO products;
```
