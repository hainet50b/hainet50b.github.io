# よく使うMySQLコマンド
{:.no_toc}

* toc
{:toc}

## ログイン
```shell
mysql -h localhost -P 3306 -u root -p

-- アクセスしているMySQLの情報を確認する
mysql> status
--------------
mysql  Ver 8.0.33 for macos13.3 on arm64 (Homebrew)

Connection id:		9
Current database:	
Current user:		root@172.17.0.1
SSL:			Cipher in use is TLS_AES_256_GCM_SHA384
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.32 MySQL Community Server - GPL
Protocol version:	10
Connection:		localhost via TCP/IP
Server characterset:	utf8mb4
Db     characterset:	utf8mb4
Client characterset:	utf8mb4
Conn.  characterset:	utf8mb4
TCP port:		3306
Binary data as:		Hexadecimal
Uptime:			35 days 11 hours 3 min 58 sec
```

## データベース
```sql
--　データベース一覧を確認する
SHOW DATABASES;

-- WHERE句で指定してデータベース一覧を確認する
SELECT schema_name FROM information_schema.schemata WHERE schema_name LIKE 'pmacho%';

-- データベースを作成する
CREATE DATABASE programacho_db;

-- データベースを選択する
USE programacho_db;

-- 現在選択しているデータベースを表示する
SELECT database();

-- データベースを削除する
-- 各種テーブルを含むすべてのオブジェクトが削除される。
DROP DATABASE programacho_db;
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

-- カラムを変更する
ALTER TABLE emp MODIFY name VARCHAR(1024);

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

# テーブルを指定してダンプを取得
mysqldump -h localhost -u root -p pmacho_db emp emp2 > pmacho_db.sql

# データベースのダンプをインポート
mysql -h localhost -u root -p < pmacho_db.sql

# PowerShellの場合はパイプを使用する
cat pmacho_db.sql | mysql -h localhost -u root -p
```

大量のデータをエクスポートして他のデータベースにインポートする場合はCSV形式が使いやすい。
```shell
# ダンプファイルを出力可能なディレクトリ一覧を確認する。
mysql> SHOW VARIABLES LIKE 'secure_file_priv';
+------------------+-----------------------+
| Variable_name    | Value                 |
+------------------+-----------------------+
| secure_file_priv | /var/lib/mysql-files/ |
+------------------+-----------------------+
1 row in set (0.00 sec)

# --tabオプションはMySQLサーバーのディレクトリを指定する必要がある。
mysqldump -h localhost -P 3306 -u root -p \
  pmacho_db pmacho_table \
  --tab=/var/lib/mysql-files \
  --fields-enclosed-by='"' \
  --fields-terminated-by=',' \
  --lines-terminated-by='\n' \
  --no-create-info

wc -l ./pmacho_table.txt
15000000 ./pmacho_table.txt

split -l 1000000 ./pmacho_table.txt pmacho_table_

ll
-rwxrwxrwx 1 hainet50b staff 196888896 Aug 21 22:32 pmacho_table_aa
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ab
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ac
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ad
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ae
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_af
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ag
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ah
-rwxrwxrwx 1 hainet50b staff 198000000 Aug 21 22:33 pmacho_table_ai
-rwxrwxrwx 1 hainet50b staff 198000001 Aug 21 22:33 pmacho_table_aj
-rwxrwxrwx 1 hainet50b staff 199000000 Aug 21 22:33 pmacho_table_ak
-rwxrwxrwx 1 hainet50b staff 199000000 Aug 21 22:33 pmacho_table_al
-rwxrwxrwx 1 hainet50b staff 199000000 Aug 21 22:33 pmacho_table_am
-rwxrwxrwx 1 hainet50b staff 199000000 Aug 21 22:34 pmacho_table_an
-rwxrwxrwx 1 hainet50b staff 199000000 Aug 21 22:34 pmacho_table_ao

wc -l ./pmacho_table_ao
1000000 ./pmacho_table_ao
```

## Bits and Pieces
```sql
-- 一定時間スリープする。
SELECT SLEEP(5);

-- ランダムな数値を生成する。
SELECT FLOOR(RAND() * 999999);

-- ランダムな文字列を生成する。
SELECT SUBSTRING(MD5(RAND()), 1, 32);
```
