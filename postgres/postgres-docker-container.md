# PostgreSQL Dockerコンテナ

[postgres - Official Image \| Docker Hub](https://hub.docker.com/_/postgres){:target="_blank"}

```shell
docker run --name postgres -d -p 5432:5432 -e POSTGRES_PASSWORD=changeme postgres:16.0
```

TODO: データ永続化
