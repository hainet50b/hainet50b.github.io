# MySQLにおけるパーティション

[Partitioning \| MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html){:target="_blank"} ([日本語版](https://dev.mysql.com/doc/refman/8.0/ja/partitioning.html){:target="_blank"})

## 月単位のパーティションを作成する
```sql
CREATE TABLE emp (
  id BIGINT AUTO_INCREMENT,
  monthly_partition CHAR(6),
  name VARCHAR(64) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
  PRIMARY KEY(id, monthly_partition)
)
PARTITION BY RANGE COLUMNS(monthly_partition) (
  PARTITION p202308 VALUES LESS THAN ('202308'),
  PARTITION p202309 VALUES LESS THAN ('202309'),
  PARTITION p202310 VALUES LESS THAN ('202310'),
  -- キャッチオールパーティションを採用する場合は最大値を指定する。
  PARTITION p999999 VALUES LESS THAN ('999999')
);
```

## パーティション確認
```sql
SELECT
  table_name,
  partition_name,
  partition_ordinal_position,
  partition_method,
  partition_expression,
  partition_description
FROM
  information_schema.partitions
WHERE
  table_name = 'pmacho_table'
  AND table_schema = 'pmacho_db';
```

## パーティション追加
```sql
ALTER TABLE pmacho_table
ADD PARTITION (
  PARTITION p202309 VALUES LESS THAN ('202309')
);

-- キャッチオールパーティションを採用している場合はREORGANIZEで対応する。
-- REORGANIZEはオンラインで実行できない場合があるのでよく確認すること。
ALTER TABLE pmacho_table
REORGANIZE PARTITION p999999 INTO (
  PARTITION p202309 VALUES LESS THAN ('202309'),
  PARTITION p999999 VALUES LESS THAN ('999999')
);
```