# よく使うMySQLコマンド
{:.no_toc}

* toc
{:toc}

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

-- ユーザーにデータベースへのアクセス権限を与える
GRANT SELECT, INSERT, UPDATE, DELETE ON programacho_db.* TO programacho_user;

-- FlywayなどでアプリケーションからDDLを発行する場合は追加の権限を与えるか専用ユーザーを発行する
GRANT CREATE, ALTER, DROP ON programacho_db.* TO programacho_user;

-- 設定を反映させる
FLUSH PRIVILEGES;
```
