# よく使うOracleコマンド
{:.no_toc}

* toc
{:toc}

## ログイン
```shell
# sysユーザー
sqlplus sys/changeme@//localhost:1521/XE as sysdba

# 一般ユーザー
sqlplus programacho/changeme@//localhost:1521/XE
```

## ユーザー（スキーマ）
```sql
-- ユーザー一覧を確認する
SELECT username FROM all_users;

-- ユーザーを作成する
CREATE USER programacho IDENTIFIED BY changeme;

-- ユーザーが所有するテーブル一覧を確認する
SELECT owner, table_name FROM all_tables WHERE owner = 'PROGRAMACHO';
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

-- テーブル定義を確認する
DESCRIBE emp;
```

## Bits and Pieces

### 一定時間スリープする
```sql
-- 一定時間スリープする
CALL DBMS_SESSION.SLEEP(5);

-- 意図的にタイムアウトを引き起こす
-- CPU性能とタイムアウト時間に合わせて数値を調整する
DECLARE
  sum_val NUMBER := 0;
BEGIN
  FOR i IN 1..100000000 LOOP
    sum_val := sum_val + i;
  END LOOP;
END;
```
