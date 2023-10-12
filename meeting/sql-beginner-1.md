# SQL Beginner #1
{:.no_toc}

* toc
{:toc}

## MySQLクライアントのインストール
パッケージマネージャー（Windows）
```powershell
# MySQLサーバーも同時にインストールされる。
scoop install mysql
```

パッケージマネージャー（Mac）
```shell
# MySQLクライアント
brew install mysql-client
```

## MySQLサーバーの起動
```shell
docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=changeme mysql:8.0.34
```

## MySQL Workbenchのインストール
パッケージマネージャー（Windows）
```powershell
scoop install mysql-workbench
```

パッケージマネージャー（Mac）
```shell
brew install mysqlworkbench
```

## テーブルにおけるリストの表現
一つの注文で複数の商品が購入されたことを複数のカラムで表現できる。

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT,
  order_id INT,
  item1 VARCHAR(32),
  item2 VARCHAR(32),
  item3 VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO orders (order_id, item1, item2, item3) VALUES (1, 'protein', 'shaker', 'spoon');

SELECT * FROM orders;
+----+----------+---------+--------+-------+
| id | order_id | item1   | item2  | item3 |
+----+----------+---------+--------+-------+
|  1 |        1 | protein | shaker | spoon |
+----+----------+---------+--------+-------+
```

しかしこれでは任意の個数の商品に対応できないため行で表現する。

```sql
CREATE TABLE orders (
  id INT AUTO_INCREMENT,
  order_id INT,
  item VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO orders (order_id, item) VALUES (1, 'protein');
INSERT INTO orders (order_id, item) VALUES (1, 'shaker');
INSERT INTO orders (order_id, item) VALUES (1, 'spoon');

SELECT * FROM orders;
+----+----------+---------+
| id | order_id | item    |
+----+----------+---------+
|  1 |        1 | protein |
|  2 |        1 | shaker  |
|  3 |        1 | spoon   |
+----+----------+---------+
```

## データベースの作成
```sql
CREATE DATABASE sql_beginner;
```

## テーブルの作成
```sql
CREATE TABLE items (
  id INT,
  name VARCHAR(100) NOT NULL,
  genre VARCHAR(32) NOT NULL,
  price INT,
  cost INT,
  registered_at DATE,
  PRIMARY KEY (id)
);

INSERT INTO items VALUES (1, 'Tシャツ', '衣服', 1000, 500, '2009-09-20');
INSERT INTO items VALUES (2, '穴あけパンチ', '事務用品', 500, 320, '2009-09-11');
INSERT INTO items VALUES (3, 'カッターシャツ', '衣服', 4000, 2800, NULL);
INSERT INTO items VALUES (4, '包丁', 'キッチン用品', 3000, 2800, '2009-09-20');
INSERT INTO items VALUES (5, '圧力鍋', 'キッチン用品', 6800, 5000, '2009-01-15');
INSERT INTO items VALUES (6, 'フォーク', 'キッチン用品', 500, NULL, '2009-09-20');
INSERT INTO items VALUES (7, 'おろしがね', 'キッチン用品', 880, 790, '2008-04-28');
INSERT INTO items VALUES (8, 'ボールペン', '事務用品', 100, NULL, '2009-11-11');
```

## 型の種類
MySQL：[第 11 章 データ型](https://dev.mysql.com/doc/refman/8.0/ja/data-types.html){:target="_blank"}
