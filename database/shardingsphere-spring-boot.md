# ShardingSphere with Spring Boot

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
      names: ds0
      ds0:
        type: com.zaxxer.hikari.HikariDataSource
        driver-class-name: org.h2.Driver
        url: jdbc:h2:mem:pmacho-db;mode=MySQL
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
            # データノードはds0に、テーブルはemp0,emp1にシャーディング
            actual-data-nodes: ds0.emp$->{0..1}
            table-strategy:
              standard:
                # empテーブルのid列でシャーディングの挙動を決定
                sharding-column: id
                # spring.shardingsphere.rules.sharding-algorithmsのtable-inlineルールを参照
                sharding-algorithm-name: table-inline
        sharding-algorithms:
          table-inline:
            type: INLINE
            props:
              # id列の偶数・奇数でシャーディング。偶数の場合はemp0、奇数の場合はemp1
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
  {"ID":18,"NAME":"a1fba7b4-b517-4d14-8d7f-1622ce62ae90"},
  {"ID":52,"NAME":"b6fd7f04-5ca7-4cb2-8565-e90fb575ec5a"},
  {"ID":56,"NAME":"07b028bf-a42a-420d-88ba-df76a21c52f7"},
  {"ID":64,"NAME":"01123929-efde-4811-9fd2-b416645a0d70"},
  {"ID":15,"NAME":"327604cd-f1b1-43e4-9d5e-f257a7a92da2"}
]
```

データベースからテーブルを確認するとemp0とemp1にシャーディングされている。

```sql
SHOW TABLES;
TABLE_NAME TABLE_SCHEMA
EMP0       PUBLIC
EMP1       PUBLIC

SELECT * FROM EMP0;
ID NAME
18 a1fba7b4-b517-4d14-8d7f-1622ce62ae90
52 b6fd7f04-5ca7-4cb2-8565-e90fb575ec5a
56 07b028bf-a42a-420d-88ba-df76a21c52f7
64 01123929-efde-4811-9fd2-b416645a0d70

SELECT * FROM EMP1;
ID NAME
15 327604cd-f1b1-43e4-9d5e-f257a7a92da2
```
