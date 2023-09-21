# Dumplingで最小限の負荷で大量データをダンプする

## 参考スキーマ
トランザクションログを保存するテーブルを想定する。  
月次でパーティション分割してパーティション単位でダンプする。

```sql
CREATE TABLE transaction_logs (
  id INT AUTO_INCREMENT,
  monthly_partition CHAR(6),
  trace_id CHAR(32),
  payload JSON,
  PRIMARY KEY (id, monthly_partition),
  INDEX transaction_logs_trace_id_index (trace_id)
)
PARTITION BY RANGE COLUMNS(monthly_partition) (
  PARTITION p202310 VALUES LESS THAN ('202310'),
  PARTITION p202311 VALUES LESS THAN ('202311'),
  PARTITION p202312 VALUES LESS THAN ('202312')
);
```

## Dumplingの動作設定を指定してダンプ
2023年10月に`p202311`パーティションにデータが継続して投入されている状況で`p202310`をダンプする。

```shell
dumpling -h localhost -P 3306 -u root -p changeme \
  --sql 'SELECT * FROM pmacho_db.transaction_logs WHERE monthly_partition = '202310';' \
  --no-schemas \
  -o ./pmacho-db/transaction-logs \
  --output-filename-template "transaction_logs.$(date "+%Y%m%d")" \
  --filetype csv \
  --no-header \
  # テーブルを分割して並列実行できる。負荷を抑えるために1にする
  --rows 1 \
  # 動作並列数を設定できる。負荷を抑えるために1にする
  --threads 1 \
  # 以下、TiDB設定
  # 特定TSOのスナップショットを指定できる。5秒前とする
  --consistency snapshot \
  --snapshot "$(date -d '-5 secs' +'%Y-%m-%d %H:%M:%S')" \
  # TiDBのメモリを指定できる。負荷を抑えるために500MBにする
  --tidb-mem-quota-query 500000000 \
  # scan操作の並列数を指定できる。負荷を抑えるために1にする
  --params "tidb_distsql_scan_concurrency=1"
```

## パーティションの削除
ダンプを確認してパーティションを削除する。

```sql
ALTER TABLE transaction_logs DROP PARTITION p202310;
```
