# SQL Beginner #5（ビュー・サブクエリ）

## ビュー
ビューはSELECT文のエイリアスであり、SELECT文の範囲ではテーブルと同様に取り扱える。

```sql
-- ビューの作成
CREATE VIEW items_cost_details (id, name, price, cost, rate)
AS
SELECT id, name, price, cost, ROUND(cost / price * 100) FROM items;

SELECT * FROM items_cost_details;
+----+-----------------------+-------+------+------+
| id | name                  | price | cost | rate |
+----+-----------------------+-------+------+------+
|  1 | Tシャツ               |  1000 |  500 |   50 |
|  2 | 穴あけパンチ          |   500 |  320 |   64 |
|  3 | カッターシャツ        |  4000 | 2800 |   70 |
|  4 | 包丁                  |  3000 | 2800 |   93 |
|  5 | 圧力鍋                |  6800 | 5000 |   74 |
|  6 | フォーク              |   500 | NULL | NULL |
|  7 | おろしがね            |   880 |  790 |   90 |
|  8 | ボールペン            |   100 | NULL | NULL |
|  9 | プロテイン            |  7500 | 3750 |   50 |
+----+-----------------------+-------+------+------+

-- ビューの削除
DROP VIEW items_cost_details;

-- ビューによるビューの参照もできるが推奨されない。
CREATE VIEW items_cost_details_high_rate (id, name, price, cost, reate)
AS
SELECT * FROM items_cost_details WHERE rate >= 70;
```

## ビューの更新
以下の2つの条件に該当しない場合はビューを経由してレコードを挿入・更新できる。
- 集約を実行していない（DISTINCT, GROUP BY）
- 複数のテーブルを参照していない（UNION）

```sql
CREATE VIEW items_view (id, name, genre, price, cost, registered_at)
AS
SELECT * FROM items;

INSERT INTO items_view VALUES (10, '商品名', '分類', 100, 50, '2023-11-01');
SELECT * FROM items WHERE id = 10;

-- 集約を実行している場合はどのような行を挿入・更新して良いか分からない。
CREATE VIEW items_view_group (genre, price_average, cost_average)
AS
SELECT genre, AVG(price), AVG(cost) FROM items GROUP BY genre;

INSERT INTO items_view_group VALUES ('分類', 100, 50); -- ?

-- 複数のテーブルを参照している場合はどのような行を挿入・更新して良いか分からない。
CREATE VIEW items_view_union (id, name, genre)
AS
SELECT id, name, genre FROM items
UNION ALL SELECT id, name, genre FROM items2;

INSERT INTO items_view_union VALUES (10, '商品名', '分類'); -- ?
```
