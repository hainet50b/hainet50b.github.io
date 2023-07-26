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

-- データベースを選択する
USE programacho_db;

-- 現在選択しているデータベースを表示する
SELECT database();
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

-- パスワードを変更する
ALTER USER programacho_user IDENTIFIED BY 'new_password';

-- 認証プラグインを変更する。MariaDB DriverでMySQL 8系に接続するときに必要となる。
ALTER USER programacho_user IDENTIFIED WITH mysql_native_password BY 'new_password';

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

-- テーブル定義を確認する
-- カラム一覧を確認する
SHOW FULL COLUMNS FROM emp;
-- DDL形式で確認する
SHOW CREATE TABLE emp;
```

## レコード
```sql
-- レコードを挿入する（INSERT）
INSERT INTO emp (name) VALUES ('hainet50b');

-- レコードを編集する（UPDATE）
UPDATE emp SET name = 'programacho' WHERE name = 'hainet50b';

-- レコードを削除する（DELETE）
DELETE FROM emp WHERE name = 'programacho';
```

## トランザクション
```sql
-- トランザクションの開始
BEGIN;

-- コミット
COMMIT;

-- ロールバック
ROLLBACK;
```

## mysqldump
```shell
# データベースのダンプを取得
mysqldump -h localhost -u root -p --databases pmacho_db > pmacho_db.sql

# データベースのダンプをインポート
mysql -h localhost -u root -p < pmacho_db.sql

# PowerShellの場合はパイプを使用する
cat pmacho_db.sql | mysql -h localhost -u root -p
```

## Bits and Pieces
```sql
-- 一定時間スリープする
SELECT SLEEP(5);
```