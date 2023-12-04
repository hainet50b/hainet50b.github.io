# SQL Index Beginner

## 参考書籍
[SQLパフォーマンス詳解: 開発者のためのデータベースチューニング解説書](https://sql-performance-explained.jp){:target="_blank"}

## 実行計画の取得
サンプルのテーブルは以下の通り。
```sql
CREATE TABLE emp (
  id INT NOT NULL,
  name VARCHAR(32) NOT NULL,
  date_of_birth DATETIME NOT NULL,
  phone_number VARCHAR(32) NOT NULL,
  PRIMARY KEY (id)
);
```

サンプルのレコードを挿入する。
```sql
INSERT INTO emp VALUES (1, 'hainet50b', '1992-10-24', '090-0000-0000');
```

プライマリキーを参照する場合の実行計画を確認する。
```sql
-- type列にconst（Bツリーの走査のみ）と記載されている。
EXPLAIN SELECT * FROM emp WHERE id = 1;
+----+-------------+-------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | emp   | NULL       | const | PRIMARY       | PRIMARY | 4       | const |    1 |   100.00 | NULL  |
+----+-------------+-------+------------+-------+---------------+---------+---------+-------+------+----------+-------+
```

## 複合インテックス
子会社との合併に伴い`id`列と`subsidiary_id`列で社員が特定できるようになったとする。
```sql
-- カラム追加
ALTER TABLE emp ADD COLUMN subsidiary_id INT NOT NULL;

-- プライマリキー削除
ALTER TABLE emp DROP PRIMARY KEY;

-- プライマリキー追加
ALTER TABLE emp ADD PRIMARY KEY (id, subsidiary_id);

-- テーブル定義の確認
DESCRIBE emp;
+---------------+-------------+------+-----+---------+-------+
| Field         | Type        | Null | Key | Default | Extra |
+---------------+-------------+------+-----+---------+-------+
| id            | int         | NO   | PRI | NULL    |       |
| name          | varchar(32) | NO   |     | NULL    |       |
| date_of_birth | datetime    | NO   |     | NULL    |       |
| phone_number  | varchar(32) | NO   |     | NULL    |       |
| subsidiary_id | int         | NO   | PRI | NULL    |       |
+---------------+-------------+------+-----+---------+-------+

-- Bツリーの走査で完結する場合には完全なプライマリキーを指定する。
EXPLAIN SELECT * FROM emp WHERE id = 1 AND subsidiary_id = 0;
+----+-------------+-------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+
| id | select_type | table | partitions | type  | possible_keys | key     | key_len | ref         | rows | filtered | Extra |
+----+-------------+-------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | emp   | NULL       | const | PRIMARY       | PRIMARY | 8       | const,const |    1 |   100.00 | NULL  |
+----+-------------+-------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+

-- 不完全なプライマリキーを指定すると実行計画が変化する。
-- type列にref（複数行が想定され、インデックスリーフノードを辿る）と記載されている。
EXPLAIN SELECT * FROM emp WHERE id = 1;
+----+-------------+-------+------------+------+---------------+---------+---------+-------+------+----------+-------+
| id | select_type | table | partitions | type | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
+----+-------------+-------+------------+------+---------------+---------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | emp   | NULL       | ref  | PRIMARY       | PRIMARY | 4       | const |    1 |   100.00 | NULL  |
+----+-------------+-------+------------+------+---------------+---------+---------+-------+------+----------+-------+

-- 子会社IDだけで検索するとtype列がALL（フルスキャン）となる。
EXPLAIN SELECT * FROM emp WHERE subsidiary_id = 0;
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | emp   | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | Using where |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

## インデックスの可視化
インデックスに含めたカラムで並べ替えすることで擬似的にインデックスを可視化できる。
```sql
SELECT id, subsidiary_id FROM emp ORDER BY id, subsidiary_id LIMIT 100;
```
