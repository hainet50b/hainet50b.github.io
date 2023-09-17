# MySQL JSON型
{:.no_toc}

* toc
{:toc}

## JSON型の取り扱い
```sql
-- JSON型を含むテーブル作成
CREATE TABLE transaction_logs (
  id INT AUTO_INCREMENT,
  payload JSON,
  PRIMARY KEY (id)
);

-- JSON型の挿入
INSERT INTO transaction_logs (payload) VALUES ('{"foo":"value"}');

SELECT * FROM transaction_logs;
+----+------------------+
| id | payload          |
+----+------------------+
|  1 | {"foo": "value"} |
+----+------------------+

-- JSON型の展開
SELECT id, payload, JSON_UNQUOTE(payload -> '$.foo') FROM transaction_logs;
+----+------------------+----------------------------------+
| id | payload          | JSON_UNQUOTE(payload -> '$.foo') |
+----+------------------+----------------------------------+
|  1 | {"foo": "value"} | value                            |
+----+------------------+----------------------------------+
```

## JSON型と仮装生成列を組み合わせたインデックスの活用
JSON型を展開して検索することもできるがインデックスが使用されない。

```sql
SELECT * FROM transaction_logs WHERE JSON_UNQUOTE(payload -> '$.foo') = 'value';
+----+------------------+
| id | payload          |
+----+------------------+
|  1 | {"foo": "value"} |
+----+------------------+

-- 実行計画はtype=ALL（フルスキャン）となる。
EXPLAIN SELECT * FROM transaction_logs WHERE JSON_UNQUOTE(payload -> '$.foo') = 'value';
+----+-------------+------------------+------------+------+---------------+------+---------+------+------+----------+-------------+
| id | select_type | table            | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra       |
+----+-------------+------------------+------------+------+---------------+------+---------+------+------+----------+-------------+
|  1 | SIMPLE      | transaction_logs | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | Using where |
+----+-------------+------------------+------------+------+---------------+------+---------+------+------+----------+-------------+
```

JSON型を展開した列を仮想生成列と定義してインデックスを作成することで解決できる。  
インデックスを追加するためディスク容量とINSERT/UPDATEの性能劣化には気をつけること。

```sql
ALTER TABLE transaction_logs
ADD COLUMN foo VARCHAR(256) GENERATED ALWAYS AS (JSON_UNQUOTE(payload -> '$.foo')) VIRTUAL;

SELECT * FROM transaction_logs WHERE foo = 'value';
+----+------------------+-------+
| id | payload          | foo   |
+----+------------------+-------+
|  1 | {"foo": "value"} | value |
+----+------------------+-------+

CREATE INDEX transaction_logs_foo_index ON transaction_logs (foo);

-- 実行計画はtransaction_logs_foo_indexを使用するものとなった。
EXPLAIN SELECT * FROM transaction_logs WHERE foo = 'value';
+----+-------------+------------------+------------+------+----------------------------+----------------------------+---------+-------+------+----------+-------+
| id | select_type | table            | partitions | type | possible_keys              | key                        | key_len | ref   | rows | filtered | Extra |
+----+-------------+------------------+------------+------+----------------------------+----------------------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | transaction_logs | NULL       | ref  | transaction_logs_foo_index | transaction_logs_foo_index | 1027    | const |    1 |   100.00 | NULL  |
+----+-------------+------------------+------------+------+----------------------------+----------------------------+---------+-------+------+----------+-------
```
