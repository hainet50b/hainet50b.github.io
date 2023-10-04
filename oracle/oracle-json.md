# Oracle JSON型
{:.no_toc}

* toc
{:toc}

## JSON型の取り扱い
```sql
-- JSON型を含むテーブル作成
CREATE TABLE transaction_logs (
  id INT,
  payload JSON,
  PRIMARY KEY (id)
);

-- JSON型の挿入
INSERT INTO transaction_logs (id, payload) VALUES (1, '{"foo":"value"}');

SELECT * FROM transaction_logs;
ID PAYLOAD        
------------------
1  {"foo":"value"}

-- JSON型の展開
SELECT id, paylaod, JSON_VALUE(payload, '$.foo') FROM transaction_logs;
ID PAYLOAD         JSON_VALUE(PAYLOAD,'$.FOO')
----------------------------------------------
1  {"foo":"value"} value                      
```

## JSON型と仮装列を組み合わせたインデックスの活用
JSON型を展開して検索することもできるがインデックスが使用されない。

```sql
SELECT * FROM transaction_logs WHERE JSON_VALUE(payload, '$.foo') = 'value';
ID PAYLOAD        
------------------
1  {"foo":"value"}

-- 実行計画はTABLE ACCESS FULL（フルスキャン）となる。
EXPLAIN PLAN FOR SELECT * FROM transaction_logs WHERE JSON_VALUE(payload, '$.foo') = 'value';
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
--------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT  |                  |     1 |  4115 |     2   (0)| 00:00:01 |
|*  1 |  TABLE ACCESS FULL| TRANSACTION_LOGS |     1 |  4115 |     2   (0)| 00:00:01 |
--------------------------------------------------------------------------------------
```

JSON型を展開した列を仮想生成列と定義してインデックスを作成することで解決できる。  
インデックスを追加するためディスク容量とINSERT/UPDATEの性能劣化には気をつけること。

```sql
ALTER TABLE transaction_logs 
ADD (foo AS (JSON_VALUE(payload, '$.foo')));

SELECT * FROM transaction_logs WHERE foo = 'value';
ID PAYLOAD         FOO  
------------------------
1  {"foo":"value"} value

CREATE INDEX transaction_logs_foo_index ON transaction_logs(foo);

-- 実行計画はTRANSACTION_LOGS_FOO_INDEXを使用するものとなった。
EXPLAIN PLAN FOR SELECT * FROM transaction_logs WHERE foo = 'value';
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY);
-----------------------------------------------------------------------------------------------------------------
|   0 | SELECT STATEMENT                    |                            |     1|  6117 |     2   (0)| 00:00:01 |
|   1 |  TABLE ACCESS BY INDEX ROWID BATCHED| TRANSACTION_LOGS           |     1|  6117 |     2   (0)| 00:00:01 |
|*  2 |   INDEX RANGE SCAN                  | TRANSACTION_LOGS_FOO_INDEX |     1|       |     1   (0)| 00:00:01 |
-----------------------------------------------------------------------------------------------------------------
```
