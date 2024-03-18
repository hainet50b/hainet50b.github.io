# MySQL 実行計画
{:.no_toc}

* toc
{:toc}

## 実行計画の取得
EXPLAIN ANALYZE句を使用することでOracleの実行計画のようにアクセスパスを表示できる。

```sql
-- SELECT
EXPLAIN ANALYZE SELECT
  ts.id,
  ts.transaction_id,
  ms.merchant_id,
  ms.merchant_name,
  tls.request_body,
  ts.created_at
FROM
  transactions AS ts
  LEFT OUTER JOIN transaction_logs AS tls ON
    ts.transaction_id = tls.transaction_id
    -- 右テーブルのフィルタリングはON句で実施する。
    AND tls.created_at >= '2024-04-01 00:00:00'
    AND tls.created_at < '2024-04-02 00:00:00'
  LEFT OUTER JOIN merchants AS ms ON
    ts.merchant_id = ms.merchant_id
WHERE
  ts.created_at >= '2024-04-01 00:00:00'
  AND ts.created_at < '2024-04-02 00:00:00';

+----+--------------------------------------+-------------+---------------+--------------+---------------------+
| id | transaction_id                       | merchant_id | merchant_name | request_body | created_at          |
+----+--------------------------------------+-------------+---------------+--------------+---------------------+
|  1 | d2b24bd7-0678-42ff-b85a-daf55fa6a473 | 00000000    | pmacho_store  | foo          | 2024-04-01 12:00:00 |
+----+--------------------------------------+-------------+---------------+--------------+---------------------+

-- EXPLAIN
EXPLAIN SELECT...

+----+-------------+-------+------------+--------+----------------------------------------------------------+----------------------+---------+-----------------------------+------+----------+-----------------------+
| id | select_type | table | partitions | type   | possible_keys                                            | key                  | key_len | ref                         | rows | filtered | Extra                 |
+----+-------------+-------+------------+--------+----------------------------------------------------------+----------------------+---------+-----------------------------+------+----------+-----------------------+
|  1 | SIMPLE      | ts    | NULL       | range  | trans_created_at_idx                                     | trans_created_at_idx | 5       | NULL                        |    1 |   100.00 | Using index condition |
|  1 | SIMPLE      | tls   | NULL       | eq_ref | transaction_id,tran_logs_id_idx,tran_logs_created_at_idx | transaction_id       | 146     | pmacho_db.ts.transaction_id |    1 |   100.00 | Using where           |
|  1 | SIMPLE      | ms    | NULL       | eq_ref | merchant_id                                              | merchant_id          | 34      | pmacho_db.ts.merchant_id    |    1 |   100.00 | NULL                  |
+----+-------------+-------+------------+--------+----------------------------------------------------------+----------------------+---------+-----------------------------+------+----------+-----------------------+

-- EXPLAIN ANALYZE（オススメ）
EXPLAIN ANALYZE SELECT...

+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| EXPLAIN                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| -> Nested loop left join  (cost=1.41 rows=1) (actual time=0.674..0.685 rows=1 loops=1)
    -> Nested loop left join  (cost=1.06 rows=1) (actual time=0.328..0.338 rows=1 loops=1)
        -> Index range scan on ts using trans_created_at_idx over ('2024-04-01 00:00:00' <= created_at < '2024-04-02 00:00:00'), with index condition: ((ts.created_at >= TIMESTAMP'2024-04-01 00:00:00') and (ts.created_at < TIMESTAMP'2024-04-02 00:00:00'))  (cost=0.71 rows=1) (actual time=0.139..0.149 rows=1 loops=1)
        -> Filter: ((`tls`.created_at >= '2024-04-01 00:00:00') and (`tls`.created_at < '2024-04-02 00:00:00'))  (cost=0.35 rows=1) (actual time=0.177..0.178 rows=1 loops=1)
            -> Single-row index lookup on tls using transaction_id (transaction_id=ts.transaction_id)  (cost=0.35 rows=1) (actual time=0.171..0.171 rows=1 loops=1)
    -> Single-row index lookup on ms using merchant_id (merchant_id=ts.merchant_id)  (cost=0.35 rows=1) (actual time=0.343..0.344 rows=1 loops=1)
 |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

## Appendix: サンプルスキーマ
```sql
-- transactions
CREATE TABLE transactions (
  id INT AUTO_INCREMENT,
  transaction_id VARCHAR(36) NOT NULL UNIQUE,
  merchant_id VARCHAR(8) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
  PRIMARY KEY (id),
  FOREIGN KEY (merchant_id) REFERENCES merchants (merchant_id)
);

CREATE INDEX trans_id_idx ON transactions (transaction_id);
CREATE INDEX trans_created_at_idx ON transactions (created_at);

INSERT INTO transactions (transaction_id, merchant_id, created_at)
VALUES
  ('d2b24bd7-0678-42ff-b85a-daf55fa6a473', '00000000', '2024-04-01 12:00:00'),
  ('b0ac585e-ddda-4bc4-8204-833fbaeaba88', '00000001', '2024-04-02 12:00:00');

-- transaction_logs
CREATE TABLE transaction_logs (
  id INT AUTO_INCREMENT,
  transaction_id VARCHAR(36) NOT NULL UNIQUE,
  request_body VARCHAR(1024),
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
  PRIMARY KEY (id),
  FOREIGN KEY (transaction_id) REFERENCES transactions (transaction_id)
);

CREATE INDEX tran_logs_id_idx ON transaction_logs (transaction_id);
CREATE INDEX tran_logs_created_at_idx ON transaction_logs (created_at);

INSERT INTO transaction_logs (transaction_id, request_body, created_at)
VALUES
  ('d2b24bd7-0678-42ff-b85a-daf55fa6a473', 'foo', '2024-04-01 12:00:01'),
  ('b0ac585e-ddda-4bc4-8204-833fbaeaba88', 'hoge', '2024-04-02 12:00:01');

-- merchants
CREATE TABLE merchants (
  id INT AUTO_INCREMENT,
  merchant_id VARCHAR(8) NOT NULL UNIQUE,
  merchant_name VARCHAR(64) NOT NULL,
  PRIMARY KEY (id)
);

INSERT INTO merchants (merchant_id, merchant_name)
VALUES
  ('00000000', 'pmacho_store'),
  ('00000001', 'hainet50b_store');
```
