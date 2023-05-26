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

### 各種コネクションの接続状況を確認する
JDBCのレイヤではTCP接続の抽象化が行われていて物理的な接続先が分からない。  
ロードバランシング状況を見る場合に以下のSQLでセッション情報からインスタンスIDを取得できる。

```sql
SELECT inst_id      AS 接続先,
       machine      AS ホスト名,
       service_name AS サービス名,
       username     AS スキーマ名,
       COUNT(*)     AS 接続数
FROM gv$session
WHERE username != 'SYS'
  AND username IS NOT NULL
GROUP BY inst_id,
         machine,
         username,
         service_name
ORDER BY inst_id;
```

### 特定のセッションを強制終了させる
`v$session`からサービスを指定してセッションの情報を取得する。  
業務ではRAC構成であることが多く、`gv$session`の`inst_id`も考慮する。

```sql
SELECT DISTINCT sid,
                serial#,
                inst_id,
                service_name,
                username,
                machine
FROM gv$session
WHERE service_name = 'pmacho'
ORDER BY inst_id,
         service_name,
         username,
         machine,
         sid,
         serial#;

SID,SERIAL#,INST_ID,SERVICE_NAME,USERNAME,MACHINE
123,12345,1,pmacho,pmacho_user,hainet50b

ALTER SYSTEM KILL SESSION '123,12345,@1';
```
