# よく使うDockerコンテナ
{:.no_toc}

* toc
{:toc}

## Debian
[debian - Official Image | Docker Hub](https://hub.docker.com/_/debian){:target="_blank"}
```shell
docker run --name debian -it debian /bin/bash
```

## Apache
[httpd - Official Image | Docker Hub](https://hub.docker.com/_/httpd){:target="_blank"}
```shell
docker run --name apache -d -p 80:80 httpd
```
```shell
curl localhost:80
```

## Nginx
[nginx - Official Image | Docker Hub](https://hub.docker.com/_/nginx){:target="_blank"}
```shell
docker run --name nginx -d -p 80:80 nginx
```
```shell
curl localhost:80
```

## MySQL
[mysql - Official Image | Docker Hub](https://hub.docker.com/_/mysql){:target="_blank"}
```shell
docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=changeme -e MYSQL_DATABASE=programacho mysql:8.0.32
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

## Oracle
以下で案内されている手順に従ってOracle Database Express EditionのDockerイメージを作成する。  
[docker-images/README.md at main · oracle/docker-images](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md){:target="_blank"}

Dockerfileのベースイメージ（Oracle Linux 7）に合わせたバイナリを使用する。  
[Oracle Database Express Edition (XE) Downloads | Oracle 日本](https://www.oracle.com/jp/database/technologies/xe-downloads.html){:target="_blank"}
![docker-containers-3266cf61b983.png](https://programacho.blob.core.windows.net/images/docker-containers-3266cf61b983.png)

```shell
git clone https://github.com/oracle/docker-images.git
curl -O https://download.oracle.com/otn-pub/otn_software/db-express/oracle-database-xe-21c-1.0-1.ol7.x86_64.rpm -L
mv oracle-database-xe-21c-1.0-1.ol7.x86_64.rpm docker-images/OracleDatabase/SingleInstance/dockerfiles/21.3.0

./docker-images/OracleDatabase/SingleInstance/dockerfiles/buildContainerImage.sh -v 21.3.0 -x

docker images
REPOSITORY        TAG         IMAGE ID       CREATED         SIZE
oracle/database   21.3.0-xe   1b4e12c74384   2 minutes ago   6.54GB
```
```shell
docker run --name oracle -d -p 1521:1521 -e ORACLE_PWD=changeme oracle/database:21.3.0-xe
```
```shell
sqlplus pdbadmin/changeme@//localhost:1521/XEPDB1
```

Apple Siliconを搭載したMacではアーキテクチャの問題でDockerイメージを作ることができなかった。  
以下のIssueを参考に、Colimaでアーキテクチャを指定したDockerデーモンを立てると上手くいった。  
[Oracle Database on Arm, including Ampere A1 on OCI and Apple M1-based Macs · oracle/docker-images · Discussion #1951](https://github.com/oracle/docker-images/discussions/1951){:target="_blank"}
```shell
brew install colima

# メモリを指定しないと2GBでDockerデーモンが立ち上がる。
# しかしそれではメモリ不足でOracleが立ち上がらなかったためメモリを4GBに指定している。
colima start --arch x86_64 --memory 4

./docker-images/OracleDatabase/SingleInstance/dockerfiles/buildContainerImage.sh -v 21.3.0 -x
```

使用後は忘れずに普段使用するDockerコンテキストに切り替えておく。
```shell
docker context use desktop-linux
desktop-linux
Current context is now "desktop-linux"

docker context list
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                       KUBERNETES ENDPOINT   ORCHESTRATOR
colima              moby                colima                                    unix:///Users/hainet50b/.colima/default/docker.sock                         
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                                 swarm
desktop-linux *     moby                                                          unix:///Users/hainet50b/.docker/run/docker.sock                             
```

## RabbitMQ
[rabbitmq - Official Image | Docker Hub](https://hub.docker.com/_/rabbitmq){:target="_blank"}
```shell
docker run --name rabbitmq -h my-rabbit -d -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:3-management
```

## Redis
[redis - Official Image | Docker Hub](https://hub.docker.com/_/redis){:target="_blank"}
```shell
docker run --name redis -d -p 6379:6379 redis
```
```shell
redis-cli
```
