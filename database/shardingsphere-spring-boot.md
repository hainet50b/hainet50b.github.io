# ShardingSphere with Spring Boot
{:.no_toc}

* toc
{:toc}

公式ドキュメント：[Use Spring Boot Starter :: ShardingSphere](https://shardingsphere.apache.org/document/5.0.0/en/user-manual/shardingsphere-jdbc/usage/sharding/spring-boot-starter/){:target="_blank"}  
サンプルリポジトリ：[shardingsphere-spring-boot-gym \| GitHub](https://github.com/hainet50b/shardingsphere-spring-boot-gym){:target="_blank"}

## 依存関係
```xml
<dependency>
    <groupId>org.apache.shardingsphere</groupId>
    <artifactId>shardingsphere-jdbc-core-spring-boot-starter</artifactId>
    <version>5.2.1</version>
</dependency>
```

## データソース設定
```yml
spring:
  shardingsphere:
    datasource:
      names: ds0,ds1
      ds0:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        url: jdbc:h2:mem:pmacho_db;mode=MySQL
        username: sa
        password:
      ds1:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        url: jdbc:h2:mem:protein_db;mode=MySQL
        username: sa
        password:
```

## シャーディング設定
```yml
spring:
  shardingsphere:
    rules:
      sharding:
        tables:
          emp:
            # データベースはds0,ds1に、テーブルはemp0,emp1,emp2にシャーディング
            actual-data-nodes: ds$->{0..1}.emp$->{0..2} 
            database-strategy:
              standard:
                # empテーブルのid列でデータベースシャーディングの挙動を決定
                sharding-column: id
                sharding-algorithm-name: database-inline
            table-strategy:
              standard:
                # empテーブルのid列でテーブルシャーディングの挙動を決定
                sharding-column: id
                sharding-algorithm-name: table-inline
        sharding-algorithms:
        database-inline:
          type: INLINE
            props:
              # id列の偶数・奇数でシャーディング。偶数の場合はds0、奇数の場合はds1
              algorithm-expression: ds${id % 2}
          table-inline:
            type: INLINE
            props:
              # id列を3で割った余りでシャーディング。
              # 3の倍数の場合はemp0、余りが1の場合はemp1、余りが2の場合はemp2
              algorithm-expression: emp${id % 2}
```

## サンプル・動作確認
`schema.sql`で以下のテーブルを作成する。

```sql
CREATE TABLE emp (
  id INT,
  name VARCHAR(36) NOT NULL,
  PRIMARY KEY (id)
);
```

以下のコードでid列が0~99の範囲でランダムに採番されたレコードを挿入する。

```java
jdbcTemplate.update(
        "INSERT INTO emp (id, name) VALUES(?, UUID())",
        new Random().nextInt(100)
);
```

アプリケーションからは透過的にデータにアクセスできる。

```java
jdbcTemplate.queryForList("SELECT * FROM emp");

[
  {"ID":12,"NAME":"9479eff8-ac47-4186-aa8d-32d959e77194"},
  {"ID":44,"NAME":"39d83dec-7526-4c22-b9a9-1d072377cd3a"},
  {"ID":62,"NAME":"d57a4e1f-9f71-4547-9dc0-2609ed1e3d2c"},
  {"ID":87,"NAME":"192fcd41-5ac5-43d5-b143-dfbcdc813317"},
  {"ID":53,"NAME":"57b98de6-5a56-4e00-b147-b789805d3146"}
]
```

データベースからテーブルを確認するとシャーディングされている。

```sql
-- pmacho_db / protein_db
SHOW TABLES;
TABLE_NAME TABLE_SCHEMA
EMP0       PUBLIC
EMP1       PUBLIC
EMP2       PUBLIC

-- pmacho_db
SELECT * FROM EMP0;
ID NAME
12 9479eff8-ac47-4186-aa8d-32d959e77194

SELECT * FROM EMP1;
(no rows, 1 ms)

SELECT * FROM EMP2;
ID NAME
44 39d83dec-7526-4c22-b9a9-1d072377cd3a
62 d57a4e1f-9f71-4547-9dc0-2609ed1e3d2c

-- protein_db
SELECT * FROM EMP0;
ID NAME
87 192fcd41-5ac5-43d5-b143-dfbcdc813317

SELECT * FROM EMP1;
(no rows, 1 ms)

SELECT * FROM EMP2;
ID NAME
53 57b98de6-5a56-4e00-b147-b789805d3146
```
