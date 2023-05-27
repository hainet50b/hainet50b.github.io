# TiDB Playground
{:.no_toc}

* toc
{:toc}

## 事前準備
参考：[tidb-course-201-lab \| GitHub](https://github.com/pingcap/tidb-course-201-lab){:target="_blank"}

```shell
git clone https://github.com/pingcap/tidb-course-201-lab.git
cd tidb-course-201-lab/scripts/

# tiupコマンドのインストール
curl --proto '=https' --tlsv1.2 -sSf https://tiup-mirrors.pingcap.com/install.sh | sh

tiup -v
1.12.1 tiup
Go Version: go1.19.9
Git Ref: v1.12.2
GitHash: dbba46be27f451b1a923c3fd64bcdfd9b254e0ec
```

## （オプション）tiupコマンドのアンインストール
tiupバイナリと環境変数の設定を削除する。

```shell
rm -rf ~/.tiup
sed -i '/tiup/d' ~/.zshrc
```

## TiDB検証環境の構築

```shell
# TiDB検証環境の起動
cat playground-start-demo.sh

#!/bin/bash
~/.tiup/bin/tiup playground v6.5.1 \
--tag demo \
--db 2 \
--pd 3 \
--kv 3 \
--tiflash 1 \
--pd.port 5379

./playground-start-demo.sh

🎉 TiDB Playground Cluster is started, enjoy!
Connect TiDB:   mysql --comments --host 127.0.0.1 --port 4000 -u root
Connect TiDB:   mysql --comments --host 127.0.0.1 --port 4001 -u root
TiDB Dashboard: http://127.0.0.1:5379/dashboard
Grafana:        http://127.0.0.1:3000
receive command: display

# TiDB検証環境の確認
cat playground-check.sh

#!/bin/bash
~/.tiup/bin/tiup playground display

./playground-check.sh

Pid    Role     Uptime
---    ----     ------
48480  pd       1m33.928359292s
48481  pd       1m33.91864625s
48482  pd       1m33.912158083s
48483  tikv     1m30.690407084s
48484  tikv     1m30.681341167s
48485  tikv     1m30.674590584s
48570  tidb     1m25.183311458s
48571  tidb     1m25.172867834s
48582  tiflash  52.77544025s
```

## TiDB検証環境の利用
MySQLコマンドでアクセスすることができる。

```shell
mysql --comments --host 127.0.0.1 --port 4000 -u root

mysql> SELECT TIDB_VERSION()\G
*************************** 1. row ***************************
TIDB_VERSION(): Release Version: v6.5.1
Edition: Community
Git Commit Hash: 4084b077d615f9dc0a41cf2e30bc6e1a02332df2
Git Branch: heads/refs/tags/v6.5.1
UTC Build Time: 2023-03-07 16:04:14
GoVersion: go1.19.5
Race Enabled: false
TiKV Min Version: 6.2.0-alpha
Check Table Before Drop: false
Store: tikv
1 row in set (0.00 sec)
```

MySQLと変わらない感覚でデータベースとテーブルを作成することができる。

```shell
mysql> CREATE DATABASE programacho;
Query OK, 0 rows affected (0.52 sec)

mysql> USE programacho;
Database changed

mysql> CREATE TABLE emp (id INT, name VARCHAR(255));
Query OK, 0 rows affected (0.52 sec)

mysql> INSERT INTO emp (id, name) VALUES (1, 'hainet50b');
Query OK, 1 row affected (0.01 sec)

mysql> SELECT * FROM emp;
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | hainet50b |
+------+-----------+
1 row in set (0.00 sec)

-- MySQL固有の機能も問題なく使用できる。
mysql> SELECT SLEEP(5);
+----------+
| SLEEP(5) |
+----------+
|        0 |
+----------+
1 row in set (5.00 sec)
```

## TiDBダッシュボード
`http://localhost:5379/dashboard`にアクセスする。パスワードは空。

![tidb-playground-0f91a2c77c2f.png](https://programacho.blob.core.windows.net/images/tidb-playground-0f91a2c77c2f.png)

QPSやLatencyを確認できる。

![tidb-playground-16d41df3d067.png](https://programacho.blob.core.windows.net/images/tidb-playground-16d41df3d067.png)
