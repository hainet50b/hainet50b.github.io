# SQL Beginner #7（集合演算・結合）

## 事前準備
```sql
CREATE TABLE foo (
  id INT,
  name VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO foo VALUES
  (1, 'foo'),
  (2, 'bar');

CREATE TABLE bar (
  id INT,
  name VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO bar VALUES
  (2, 'bar'),
  (3, 'baz');
```

## レコードの集合演算
列が同じ二次元表を2つ受けて、異なる二次元表を1つ作成する関数とみなせる。

```sql
-- 和
SELECT * FROM foo;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
+----+------+

SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  2 | bar  |
|  3 | baz  |
+----+------+

-- 重複行（3, baz）は取り除かれる。
SELECT * FROM foo
UNION
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
|  3 | baz  |
+----+------+

-- ALLオプションを付与すると重複行を取り除かない。
SELECT * FROM foo
UNION ALL
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
|  2 | bar  |
|  3 | baz  |
+----+------+

-- 列が一致している必要がある。
SELECT id, name, 1 FROM foo
UNION
SELECT * FROM bar;
ERROR 1222 (21000): The used SELECT statements have a different number of columns

-- ORDER BY句は集合演算結果となる二次元表にのみ適用できる。
SELECT * FROM foo
UNION
SELECT * FROM bar
ORDER BY id DESC;
+----+------+
| id | name |
+----+------+
|  3 | baz  |
|  2 | bar  |
|  1 | foo  |
+----+------+

-- 積
SELECT * FROM foo
INTERSECT
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  2 | bar  |
+----+------+

-- 差
SELECT * FROM foo
EXCEPT
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
+----+------+

-- 差は和や積と異なり記述順序によって結果が変わる。
SELECT * FROM bar
EXCEPT
SELECT * FROM foo;
+----+------+
| id | name |
+----+------+
|  3 | baz  |
+----+------+

-- 結合した二次元表をサブクエリとして取り扱うことで射影や選択することができる。
SELECT
 name
FROM (
  SELECT * FROM foo
  UNION
  SELECT * FROM bar
) AS users
WHERE
  id = 1;
```

## レコードの結合

### 事前準備
```sql
CREATE TABLE shops (
  id INT,
  name VARCHAR(32) NOT NULL,
  PRIMARY KEY (id)
);

INSERT INTO shops VALUES
  (1, '東京'),
  (2, '名古屋'),
  (3, '大阪'),
  (4, '福岡');

CREATE TABLE stock (
  id INT AUTO_INCREMENT,
  shop_id INT,
  item_id INT,
  amount INT,
  PRIMARY KEY (id),
  UNIQUE (shop_id, item_id)
);

INSERT INTO stock (shop_id, item_id, amount) VALUES
  (1, 1, 30),
  (1, 2, 50),
  (1, 3, 15),
  (2, 2, 30),
  (2, 3, 120),
  (2, 4, 20),
  (2, 6, 10),
  (2, 7, 40),
  (3, 3, 20),
  (3, 4, 50),
  (3, 6, 90),
  (3, 7, 70),
  (4, 1, 110);
```

### 内部結合（INNER JOIN）
```sql
-- 在庫テーブルの店舗IDと店舗テーブルのIDで結合する。
SELECT
  *
FROM
  stock
  INNER JOIN shops
  ON stock.shop_id = shops.id;

-- 更にこの二次元表の在庫テーブルの商品IDと商品テーブルのIDで結合する。
SELECT
  *
FROM
  stock
  INNER JOIN shops
  ON stock.shop_id = shops.id
  INNER JOIN items
  ON stock.item_id = items.id;
+----+---------+---------+--------+----+-----------+----+-----------------------+--------------------+-------+------+---------------+
| id | shop_id | item_id | amount | id | name      | id | name                  | genre              | price | cost | registered_at |
+----+---------+---------+--------+----+-----------+----+-----------------------+--------------------+-------+------+---------------+
|  1 |       1 |       1 |     30 |  1 | 東京      |  1 | Tシャツ               | 衣服               |  1000 |  500 | 2009-09-20    |
|  2 |       1 |       2 |     50 |  1 | 東京      |  2 | 穴あけパンチ          | 事務用品           |   500 |  320 | 2009-09-11    |
|  3 |       1 |       3 |     15 |  1 | 東京      |  3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |
|  4 |       2 |       2 |     30 |  2 | 名古屋    |  2 | 穴あけパンチ          | 事務用品           |   500 |  320 | 2009-09-11    |
|  5 |       2 |       3 |    120 |  2 | 名古屋    |  3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |
|  6 |       2 |       4 |     20 |  2 | 名古屋    |  4 | 包丁                  | キッチン用品       |  3000 | 2800 | 2009-09-20    |
|  7 |       2 |       6 |     10 |  2 | 名古屋    |  6 | フォーク              | キッチン用品       |   500 | NULL | 2009-09-20    |
|  8 |       2 |       7 |     40 |  2 | 名古屋    |  7 | おろしがね            | キッチン用品       |   880 |  790 | 2008-04-28    |
|  9 |       3 |       3 |     20 |  3 | 大阪      |  3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |
| 10 |       3 |       4 |     50 |  3 | 大阪      |  4 | 包丁                  | キッチン用品       |  3000 | 2800 | 2009-09-20    |
| 11 |       3 |       6 |     90 |  3 | 大阪      |  6 | フォーク              | キッチン用品       |   500 | NULL | 2009-09-20    |
| 12 |       3 |       7 |     70 |  3 | 大阪      |  7 | おろしがね            | キッチン用品       |   880 |  790 | 2008-04-28    |
| 13 |       4 |       1 |    110 |  4 | 福岡      |  1 | Tシャツ               | 衣服               |  1000 |  500 | 2009-09-20    |
+----+---------+---------+--------+----+-----------+----+-----------------------+--------------------+-------+------+---------------+

-- ON句で指定した結合キーを射影で取り除く。
SELECT
  stock.shop_id,
  shops.name,
  stock.item_id,
  items.name,
  items.genre,
  items.price,
  items.cost,
  items.registered_at,
  stock.amount
FROM
  stock
  INNER JOIN shops
  ON stock.shop_id = shops.id
  INNER JOIN items
  ON stock.item_id = items.id;
+---------+-----------+---------+-----------------------+--------------------+-------+------+---------------+--------+
| shop_id | name      | item_id | name                  | genre              | price | cost | registered_at | amount |
+---------+-----------+---------+-----------------------+--------------------+-------+------+---------------+--------+
|       1 | 東京      |       1 | Tシャツ               | 衣服               |  1000 |  500 | 2009-09-20    |     30 |
|       1 | 東京      |       2 | 穴あけパンチ          | 事務用品           |   500 |  320 | 2009-09-11    |     50 |
|       1 | 東京      |       3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |     15 |
|       2 | 名古屋    |       2 | 穴あけパンチ          | 事務用品           |   500 |  320 | 2009-09-11    |     30 |
|       2 | 名古屋    |       3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |    120 |
|       2 | 名古屋    |       4 | 包丁                  | キッチン用品       |  3000 | 2800 | 2009-09-20    |     20 |
|       2 | 名古屋    |       6 | フォーク              | キッチン用品       |   500 | NULL | 2009-09-20    |     10 |
|       2 | 名古屋    |       7 | おろしがね            | キッチン用品       |   880 |  790 | 2008-04-28    |     40 |
|       3 | 大阪      |       3 | カッターシャツ        | 衣服               |  4000 | 2800 | NULL          |     20 |
|       3 | 大阪      |       4 | 包丁                  | キッチン用品       |  3000 | 2800 | 2009-09-20    |     50 |
|       3 | 大阪      |       6 | フォーク              | キッチン用品       |   500 | NULL | 2009-09-20    |     90 |
|       3 | 大阪      |       7 | おろしがね            | キッチン用品       |   880 |  790 | 2008-04-28    |     70 |
|       4 | 福岡      |       1 | Tシャツ               | 衣服               |  1000 |  500 | 2009-09-20    |    110 |
+---------+-----------+---------+-----------------------+--------------------+-------+------+---------------+--------+

-- 結合で出来上がった二次元表は自由に選択・射影を実施できる。
SELECT
  shops.name,
  items.name,
  stock.amount
FROM
  stock
  INNER JOIN shops
  ON stock.shop_id = shops.id
  INNER JOIN items
  ON stock.item_id = items.id
WHERE
  shops.id = 1;

-- 2つのテーブルの掛け算で算出した各行に対して、ON句の条件で選択を実施する。
-- 以下の実用的でない例では、すべての在庫と店舗を掛け算した結果から条件に見合うものを選択している。
SELECT
  stock.shop_id AS "stock.shop_id",
  shops.id AS "shops.id",
  shops.name,
  stock.item_id,
  stock.amount
FROM
  stock
  INNER JOIN shops
  ON stock.shop_id > shops.id;
+---------------+----------+-----------+---------+--------+
| stock.shop_id | shops.id | name      | item_id | amount |
+---------------+----------+-----------+---------+--------+
|             2 |        1 | 東京      |       2 |     30 |
|             2 |        1 | 東京      |       3 |    120 |
|             2 |        1 | 東京      |       4 |     20 |
|             2 |        1 | 東京      |       6 |     10 |
|             2 |        1 | 東京      |       7 |     40 |
|             3 |        1 | 東京      |       3 |     20 |
|             3 |        1 | 東京      |       4 |     50 |
|             3 |        1 | 東京      |       6 |     90 |
|             3 |        1 | 東京      |       7 |     70 |
|             4 |        1 | 東京      |       1 |    110 |
|             3 |        2 | 名古屋    |       3 |     20 |
|             3 |        2 | 名古屋    |       4 |     50 |
|             3 |        2 | 名古屋    |       6 |     90 |
|             3 |        2 | 名古屋    |       7 |     70 |
|             4 |        2 | 名古屋    |       1 |    110 |
|             4 |        3 | 大阪      |       1 |    110 |
+---------------+----------+-----------+---------+--------+
```

### 外部結合（OUTER JOIN）
```sql
```
