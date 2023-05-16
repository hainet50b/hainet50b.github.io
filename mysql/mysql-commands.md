# よく使うMySQLコマンド
{:.no_toc}

* toc
{:toc}

## ログイン
```shell
mysql -h localhost -P 3306 -u root -p
```

## データベース
```sql
--　データベース一覧を確認する
SHOW DATABASES;

-- データベースを作成する
CREATE DATABASE programacho_db;
```

## ユーザー
```sql
-- ユーザー一覧を確認する
SELECT user, host FROM mysql.user;

-- ユーザーを作成する
CREATE USER programacho_user;

-- パスワードを指定できる
CREATE USER programacho_user IDENTIFIED BY 'changeme';

-- アクセス元のホスト名を指定できる
CREATE USER programacho_user@localhost;

-- ユーザーを削除する
DROP USER programacho_user;
```

## 権限
権限一覧：[MySQLで提供される権限](https://dev.mysql.com/doc/refman/8.0/ja/privileges-provided.html){:target="_blank"}

```sql
-- ユーザーの権限を確認する
SHOW GRANTS FOR programacho_user;

-- ユーザーにデータベースへのアクセス権限を付与する
GRANT SELECT, INSERT, UPDATE, DELETE ON programacho_db.* TO programacho_user;

-- FlywayなどでアプリケーションからDDLを発行する場合は追加の権限を付与するか専用ユーザーを発行する
GRANT CREATE, ALTER, DROP ON programacho_db.* TO programacho_user;

-- 設定を反映させる
FLUSH PRIVILEGES;
```

## テーブル
```sql
-- テーブルを作成する
CREATE TABLE emp (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

-- テーブル定義を確認する（DESCTIBEとEXPLAINはSHOW COLUMNSのエイリアス）
SHOW COLUMNS FROM emp;
DESCRIBE emp;
EXPLAIN emp;
```

## Bits and Pieces
```sql
-- 一定時間スリープする
SELECT SLEEP(5);
```