# SQL Beginner #2（選択と射影、演算子）
{:.no_toc}

* toc
{:toc}

## テーブルを出力する
SELECT"句"、FROM"句"と句という表現をすることが一般的。

```sql
SELECT * FROM items;
```

## 列を選択してテーブルを出力する（射影）
```sql
SELECT id, name FROM items;
+----+-----------------------+
| id | name                  |
+----+-----------------------+
|  1 | Tシャツ               |
|  2 | 穴あけパンチ          |
|  3 | カッターシャツ        |
|  4 | 包丁                  |
|  5 | 圧力鍋                |
|  6 | フォーク              |
|  7 | おろしがね            |
|  8 | ボールペン            |
|  9 | プロテイン            |
+----+-----------------------+
```

## 行を選択してテーブルを出力する（選択）
```sql
SELECT * FROM items WHERE id = 1;
+----+------------+--------+-------+------+---------------+
| id | name       | genre  | price | cost | registered_at |
+----+------------+--------+-------+------+---------------+
|  1 | Tシャツ    | 衣服   |  1000 |  500 | 2009-09-20    |
+----+------------+--------+-------+------+---------------+
```

## 列に別名を付与
ORMでPOJOを調整するのではなくAS句で出力する結果セットを調整する手段もある。

```sql
SELECT
  id,
  name AS 商品名,
  genre AS ジャンル,
  price AS 価格,
  cost AS 原価,
  registered_at AS 登録日
FROM items
WHERE id = 1;
+----+------------+--------------+--------+--------+------------+
| id | 商品名     | ジャンル     | 価格   | 原価   | 登録日     |
+----+------------+--------------+--------+--------+------------+
|  1 | Tシャツ    | 衣服         |   1000 |    500 | 2009-09-20 |
+----+------------+--------------+--------+--------+------------+
```

## 定数の出力
```sql
SELECT 1 AS constant;
+----------+
| constant |
+----------+
|        1 |
+----------+

SELECT id, name, 1 AS constant FROM items WHERE id IN (1, 2, 3);
+----+-----------------------+----------+
| id | name                  | constant |
+----+-----------------------+----------+
|  1 | Tシャツ               |        1 |
|  2 | 穴あけパンチ          |        1 |
|  3 | カッターシャツ        |        1 |
+----+-----------------------+----------+
```

## 重複の排除
```sql
SELECT DISTINCT genre FROM items;
+--------------------+
| genre              |
+--------------------+
| 衣服               |
| 事務用品           |
| キッチン用品       |
| 食品               |
+--------------------+

-- 複数の列の組み合わせで重複を排除できる。
SELECT DISTINCT genre, registered_at FROM items;
+--------------------+---------------+
| genre              | registered_at |
+--------------------+---------------+
| 衣服               | 2009-09-20    |
| 事務用品           | 2009-09-11    |
| 衣服               | NULL          |
| キッチン用品       | 2009-09-20    |
| キッチン用品       | 2009-01-15    |
| キッチン用品       | 2008-04-28    |
| 事務用品           | 2009-11-11    |
| 食品               | 2023-10-14    |
+--------------------+---------------+
```

## 選択と射影の順序
以下のSQLでは`genre = '衣服'`の条件式が行単位に適用されて選択された後に射影が行われる。

```sql
SELECT id, name, genre FROM items WHERE genre = '衣服';
+----+-----------------------+--------+
| id | name                  | genre  |
+----+-----------------------+--------+
|  1 | Tシャツ               | 衣服   |
|  3 | カッターシャツ        | 衣服   |
+----+-----------------------+--------+
```

## WHERE 1 / WHERE 0
WHERE句は行単位で論理式を判定して真となった行を選択する。

```sql
SELECT COUNT(*) FROM items WHERE 1; -- 9
SELECT COUNT(*) FROM items WHERE 0; -- 0
SELECT COUNT(*) FROM items WHERE true; -- 9
SELECT COUNT(*) FROM items WHERE false; -- 0
```

## MySQLにおける真偽値（TRUE/FALSE）
MySQLには厳密には真偽値の表現にTINYINT型（2^8までを表す整数値）を使用する。  
BOOLEANキーワードはTINYINT(1)（1は表示幅）のエイリアスとして定義されている。

```sql
-- TRUEは1, FALSEは0のエイリアスとして定義されている。
SELECT TRUE; -- 1
SELECT FALSE; -- 0

-- 実用ではないが、TRUEは確かに1として機能する。
SELECT TRUE;
+------+
| TRUE |
+------+
|    1 |
+------+

SELECT * FROM items WHERE id = TRUE;
+----+------------+--------+-------+------+---------------+
| id | name       | genre  | price | cost | registered_at |
+----+------------+--------+-------+------+---------------+
|  1 | Tシャツ    | 衣服   |  1000 |  500 | 2009-09-20    |
+----+------------+--------+-------+------+---------------+
```

## コメントの記述
```sql
-- コメントを記述できる。
SELECT * FROM items;

/* 複数行の
   コメントを記述できる。*/
SELECT * FROM items;

SELECT * FROM items; -- SQL文の後にもコメントを記述できる。

SELECT
  id, -- 複数行のSQL文の各行にも
  name -- コメントを記述できる。
FROM items;
```

## 算術演算子
```sql
SELECT id, name, cost / price AS 原価率 FROM items;
+----+-----------------------+-----------+
| id | name                  | 原価率    |
+----+-----------------------+-----------+
|  1 | Tシャツ               |    0.5000 |
|  2 | 穴あけパンチ          |    0.6400 |
|  3 | カッターシャツ        |    0.7000 |
|  4 | 包丁                  |    0.9333 |
|  5 | 圧力鍋                |    0.7353 |
|  6 | フォーク              |      NULL |
|  7 | おろしがね            |    0.8977 |
|  8 | ボールペン            |      NULL |
|  9 | プロテイン            |    0.5000 |
+----+-----------------------+-----------+

SELECT 1 + 1; -- 2
SELECT 1 - 1; -- 0
SELECT 1 * 2; -- 2
SELECT 2 / 2; -- 1.000

SELECT NULL + 1; -- NULL
SELECT NULL - 1; -- NULL
SELECT 1 * NULL; -- NULL
SELECT 1 / NULL; -- NULL

SELECT 1 / 0; -- NULL（データベースにより挙動が異なる可能性がある）
```

## 比較演算子
```sql
SELECT id, name, price FROM items WHERE price = 500;
+----+--------------------+-------+
| id | name               | price |
+----+--------------------+-------+
|  2 | 穴あけパンチ       |   500 |
|  6 | フォーク           |   500 |
+----+--------------------+-------+

SELECT id, name, price FROM items WHERE price != 500;
+----+-----------------------+-------+
| id | name                  | price |
+----+-----------------------+-------+
|  1 | Tシャツ               |  1000 |
|  3 | カッターシャツ        |  4000 |
|  4 | 包丁                  |  3000 |
|  5 | 圧力鍋                |  6800 |
|  7 | おろしがね            |   880 |
|  8 | ボールペン            |   100 |
|  9 | プロテイン            |  7500 |
+----+-----------------------+-------+

SELECT 1 = 1; -- 1
SELECT 1 != 1; -- 0
SELECT 1 <> 1; -- 0
SELECT 1 >= 1; -- 1
SELECT 1 > 1; -- 0
SELECT 1 <= 1; -- 1
SELECT 1 < 1; -- 0
```

## 文字列と不等号
```sql
CREATE TABLE characters (
  value CHAR(3),
  PRIMARY KEY (value)
);

INSERT INTO characters VALUES ('1');
INSERT INTO characters VALUES ('2');
INSERT INTO characters VALUES ('3');
INSERT INTO characters VALUES ('11');
INSERT INTO characters VALUES ('12');
INSERT INTO characters VALUES ('21');
INSERT INTO characters VALUES ('22');
INSERT INTO characters VALUES ('31');
INSERT INTO characters VALUES ('32');

-- '11'や'12'は選択されない。
SELECT * FROM characters WHERE value >= '2';
+-------+
| value |
+-------+
| 2     |
| 21    |
| 22    |
| 3     |
| 31    |
| 32    |
+-------+

-- ハイフンを添えて本の章立てに見立てると見通しが良くなる。
DELETE FROM characters;

INSERT INTO characters VALUES ('1');
INSERT INTO characters VALUES ('1-1');
INSERT INTO characters VALUES ('1-2');
INSERT INTO characters VALUES ('2');
INSERT INTO characters VALUES ('2-1');
INSERT INTO characters VALUES ('2-2');
INSERT INTO characters VALUES ('3');
INSERT INTO characters VALUES ('3-1');
INSERT INTO characters VALUES ('3-2');

SELECT * FROM characters WHERE value >= '2';
+-------+
| value |
+-------+
| 2     |
| 2-1   |
| 2-2   |
| 3     |
| 3-1   |
| 3-2   |
+-------+
```

## NULLと等号
```sql
-- 500円の商品とそうでない商品を合わせても7件しか選択されない。
SELECT COUNT(*) FROM items WHERE cost = 500; -- 1
SELECT COUNT(*) FROM items WHERE cost != 500; -- 6

SELECT COUNT(*) FROM items WHERE cost = NULL; -- 0

SELECT id, name, cost FROM items WHERE cost IS NULL;
+----+-----------------+------+
| id | name            | cost |
+----+-----------------+------+
|  6 | フォーク        | NULL |
|  8 | ボールペン      | NULL |
+----+-----------------+------+

SELECT id, name, cost FROM items WHERE cost IS NOT NULL;
+----+-----------------------+------+
| id | name                  | cost |
+----+-----------------------+------+
|  1 | Tシャツ               |  500 |
|  2 | 穴あけパンチ          |  320 |
|  3 | カッターシャツ        | 2800 |
|  4 | 包丁                  | 2800 |
|  5 | 圧力鍋                | 5000 |
|  7 | おろしがね            |  790 |
|  9 | プロテイン            | 3750 |
+----+-----------------------+------+
```

## 論理演算子
```sql
SELECT NOT 1; -- 0

-- 以下の2つのクエリは同じ結果を返す。
SELECT COUNT(*) FROM items WHERE price != 500; -- 7
SELECT COUNT(*) FROM items WHERE NOT price = 500; -- 7

SELECT 1 AND 1; -- 1
SELECT 1 OR 0; -- 1
SELECT 1 XOR 1; -- 0

-- NANDは存在していないのでNOTと組み合わせて表現する。
SELECT NOT (1 AND 1); -- 0

SELECT id, name, genre, price FROM items
WHERE
  genre = 'キッチン用品'
  AND price > 2000;
+----+-----------+--------------------+-------+
| id | name      | genre              | price |
+----+-----------+--------------------+-------+
|  4 | 包丁      | キッチン用品       |  3000 |
|  5 | 圧力鍋    | キッチン用品       |  6800 |
+----+-----------+--------------------+-------+

-- カッコを付与することで論理演算の優先順位を変更できる。
SELECT id, name, genre, registered_at FROM items
WHERE
  genre = '事務用品'
  AND (
    registered_at = '2009-09-11'
    OR registered_at = '2009-09-20'
  );
+----+--------------------+--------------+---------------+
| id | name               | genre        | registered_at |
+----+--------------------+--------------+---------------+
|  2 | 穴あけパンチ       | 事務用品     | 2009-09-11    |
+----+--------------------+--------------+---------------+

-- カッコを付与しないと意図しない対象が選択される。
SELECT id, name, genre, registered_at FROM items
WHERE
  genre = '事務用品'
  AND registered_at = '2009-09-11'
  OR registered_at = '2009-09-20'
;
+----+--------------------+--------------------+---------------+
| id | name               | genre              | registered_at |
+----+--------------------+--------------------+---------------+
|  1 | Tシャツ            | 衣服               | 2009-09-20    |
|  2 | 穴あけパンチ       | 事務用品           | 2009-09-11    |
|  4 | 包丁               | キッチン用品       | 2009-09-20    |
|  6 | フォーク           | キッチン用品       | 2009-09-20    |
+----+--------------------+--------------------+---------------+

-- 論理演算にNULLを含めるとNULLとなるためSQLにおいては0,1にNULLを加えた3値論理となる。
SELECT 1 = NULL; -- NULL
```
