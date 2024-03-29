# MySQL パーティション管理

[Partitioning \| MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/partitioning.html){:target="_blank"} ([日本語版](https://dev.mysql.com/doc/refman/8.0/ja/partitioning.html){:target="_blank"})

## パーティション作成
以下の例では月次パーティションを作成している。

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

動的なパーティション振り分けも可能であるが、運用上使用しない。
```sql
CREATE TABLE emp (
  id BIGINT AUTO_INCREMENT,
  name VARCHAR(64) NOT NULL,
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
  PRIMARY KEY(id, created_at)
)
PARTITION BY RANGE (YEAR(created_at) * 100 + MONTH(created_at)) (
  PARTITION p202401 VALUES LESS THAN (202401),
  PARTITION p202402 VALUES LESS THAN (202402),
  PARTITION p202403 VALUES LESS THAN (202403)
);
```

## パーティション追加
```sql
ALTER TABLE pmacho_table
ADD PARTITION (
  PARTITION p202309 VALUES LESS THAN ('202309')
);

-- 名前が重複するパーティションは追加できない。
mysql> ALTER TABLE emp
    -> ADD PARTITION (
    ->   PARTITION p202310 VALUES LESS THAN ('202310')
    -> );
ERROR 1517 (HY000): Duplicate partition name p202310

-- 範囲が適切でないパーティションは追加できない。
mysql> ALTER TABLE emp
    -> ADD PARTITION (
    ->   PARTITION p999999 VALUES LESS THAN ('202310')
    -> );
ERROR 1493 (HY000): VALUES LESS THAN value must be strictly increasing for each partition

-- キャッチオールパーティションを採用している場合はREORGANIZEで対応する。
-- REORGANIZEはオンラインで実行できない場合があるのでよく確認すること。
ALTER TABLE pmacho_table
REORGANIZE PARTITION p999999 INTO (
  PARTITION p202309 VALUES LESS THAN ('202309'),
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
  table_schema = 'pmacho_db'
  AND table_name = 'pmacho_table';
```

## レコードの追加
```sql
-- 該当するパーティションがない場合は正常にINSERTできない。
INSERT INTO emp (monthly_partition, name) VALUES ('202401', 'hainet50b');
ERROR 1526 (HY000): Table has no partition for value from column_list

-- Spring BootのJdbcTemplateではSQLExceptionが発生する。
Caused by: org.springframework.jdbc.UncategorizedSQLException: StatementCallback; uncategorized SQLException for SQL [INSERT INTO emp (monthly_partition, name) VALUES ('202401', 'hainet50b')]; SQL state [HY000]; error code [1526]; Table has no partition for value from column_list
at org.springframework.jdbc.core.JdbcTemplate.translateException(JdbcTemplate.java:1581) ~[spring-jdbc-6.0.11.jar:6.0.11]
at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:398) ~[spring-jdbc-6.0.11.jar:6.0.11]
at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:436) ~[spring-jdbc-6.0.11.jar:6.0.11]
at com.programacho.nopartitiongym.NoPartitionGymApplication.lambda$run$0(NoPartitionGymApplication.java:25) ~[classes/:na]
at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:771) ~[spring-boot-3.1.2.jar:3.1.2]
... 5 common frames omitted
Caused by: java.sql.SQLException: Table has no partition for value from column_list
at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:130) ~[mysql-connector-j-8.0.33.jar:8.0.33]
at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122) ~[mysql-connector-j-8.0.33.jar:8.0.33]
at com.mysql.cj.jdbc.StatementImpl.executeInternal(StatementImpl.java:763) ~[mysql-connector-j-8.0.33.jar:8.0.33]
at com.mysql.cj.jdbc.StatementImpl.execute(StatementImpl.java:648) ~[mysql-connector-j-8.0.33.jar:8.0.33]
at com.zaxxer.hikari.pool.ProxyStatement.execute(ProxyStatement.java:94) ~[HikariCP-5.0.1.jar:na]
at com.zaxxer.hikari.pool.HikariProxyStatement.execute(HikariProxyStatement.java) ~[HikariCP-5.0.1.jar:na]
at org.springframework.jdbc.core.JdbcTemplate$1ExecuteStatementCallback.doInStatement(JdbcTemplate.java:427) ~[spring-jdbc-6.0.11.jar:6.0.11]
at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:383) ~[spring-jdbc-6.0.11.jar:6.0.11]
... 8 common frames omitted
```

## パーティションの削除
```sql
-- パーティションを削除し、パーティション内のレコードもすべて削除する。
ALTER TABLE emp DROP PARTITION p202308;

-- パーティションを維持し、パーティション内のレコードをすべて削除する。
ALTER TABLE emp TRUNCATE PARTITION p202308;
```