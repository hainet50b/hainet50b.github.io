# MySQL Dockerコンテナ

[mysql - Official Image \| Docker Hub](https://hub.docker.com/_/mysql){:target="_blank"}

```shell
docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=changeme mysql:8.0.32
```

データを永続化するにはボリュームをアタッチする。  
開発用途ではユーザーディレクトリ配下に配置すると取り回しやすい。
```
-v $HOME/Develop/mysql:/var/lib/mysql
```

MySQLクライアントはlocalhostに対してデフォルトでUNIXドメインソケットで接続する。  
Dockerで立ち上げるとソケットファイルが存在しないためプロトコルを指定してTCPで接続する必要がある。
```shell
mysql -h localhost -P 3306 --protocol TCP -u root -p
```

プロトコルを指定しないと以下のようなエラーが発生する。
```shell
mysql -h localhost -P 3306 -u root -p

Enter password:
ERROR 2002 (HY000): Can\'t connect to local MySQL server through socket \'/tmp/mysql.sock\' (2)
```
