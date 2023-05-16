# よく使うOracleコマンド

## ユーザー（スキーマ）
```sql
-- ユーザー一覧を確認する
SELECT username FROM all_users;

-- ユーザーが所有するテーブル一覧を確認する
SELECT owner, table_name FROM all_tables WHERE owner = 'PROGRAMACHO';

-- ユーザーを作成する
CREATE USER programacho IDENTIFIED BY changeme;
```

## 権限
```sql
-- ユーザーの権限を確認する
SELECT * FROM dba_sys_privs WHERE grantee = 'PROGRAMACHO';

-- ユーザーにログイン権限を付与する
GRANT CREATE SESSION TO programacho;

-- ユーザーに表領域を割り当てる（CREATE TABLEやINSERTを行えるようにする）
ALTER USER programacho QUOTA 100M ON users;

-- シーケンスを参照する権限を付与する（GENERATED ALWAYS AS IDENTITYで使う）
GRANT CREATE ANY SEQUENCE, SELECT ANY SEQUENCE TO programacho;

-- FlywayなどでアプリケーションからDDLを発行する場合は追加の権限を付与するか専用ユーザーを発行する
GRANT CREATE TABLE, ALTER ANY TABLE, DROP ANY TABLE TO programacho;
```

## テーブル
```sql
-- テーブルを作成する
CREATE TABLE emp (
    id NUMBER GENERATED ALWAYS AS IDENTITY,
    name VARCHAR(255) NOT NULL,
    CONSTRAINT pk PRIMARY KEY(id)
);
```
