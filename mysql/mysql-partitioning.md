# MySQLにおけるパーティション

[Partitioning \| MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html){:target="_blank"} ([日本語版](https://dev.mysql.com/doc/refman/8.0/ja/partitioning.html){:target="_blank"})

## 月単位のパーティションを作成する
```sql
CREATE TABLE emp (
  id BIGINT AUTO_INCREMENT,
  name VARCHAR(64) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
  partition_key CHAR(6) NOT NULL DEFAULT DATE_FORMAT(NOW(), '%Y%m'),
  PRIMARY KEY(id, monthly_partition)
)
PARTITION BY RANGE COLUMNS(partition_key) (
  PARTITION p202308 VALUES LESS THAN ('202308'),
  PARTITION p202309 VALUES LESS THAN ('202309'),
  PARTITION p202310 VALUES LESS THAN ('202310'),
  -- パーティション追加運用が漏れても最低限動作することを保証する。
  PARTITION p999999 VALUES LESS THAN ('999999')
);
```