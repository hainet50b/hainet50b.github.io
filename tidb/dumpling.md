# Dumpling（MySQL/TiDBエクスポートツール）

## Dumplingのインストール
```shell
# Mac
brew install dumpling
```

## データベースのエクスポート
```shell
# データベース全体のエクスポート
dumpling -h localhost -P 3306 -u root -p changeme \
  -B pmacho_db \
  --filetype sql \
  --threads 2 \
  -o ./dumpling \
  -F 256MiB

# テーブルのエクスポート
# -Tオプションはテーブルをカンマ区切りで複数指定できる。
dumpling -h localhost -P 3306 -u root -p changeme \
  -T pmacho_db.emp \
  --filetype sql \
  --threads 2 \
  -o ./dumpling \
  -F 256MiB

# 論理的にデータを指定してエクスポート
dumpling -h localhost -P 3306 -u root -p changeme \
  -T pmacho_db.emp \
  --where 'monthly_partition = '202308'' \
  --filetype sql \
  --threads 2 \
  -o ./dumpling \
  -F 256MiB

# WHERE句でフィルタされたファイルが出力される。
cat dumpling/pmacho_db.emp.000000000.sql
/*!40014 SET FOREIGN_KEY_CHECKS=0*/;
/*!40101 SET NAMES binary*/;
INSERT INTO `emp` VALUES
(10,'202308','hainet50b','2023-08-13 13:09:36');

# WHERE句から外れたレコードは出力されていない。
mysql> SELECT * FROM emp;
+----+-------------------+-----------+---------------------+
| id | monthly_partition | name      | created_at          |
+----+-------------------+-----------+---------------------+
| 10 | 202308            | hainet50b | 2023-08-13 13:09:36 |
| 11 | 202309            | hainet50b | 2023-08-13 13:09:40 |
| 12 | 202310            | hainet50b | 2023-08-13 13:09:45 |
+----+-------------------+-----------+---------------------+
```