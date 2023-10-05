# RabbitMQ Dockerコンテナ

[rabbitmq - Official Image \| Docker Hub](https://hub.docker.com/_/rabbitmq){:target="_blank"}

```shell
docker run --name rabbitmq -h my-rabbit -d -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:3-management
```

RabbitMQ Management Pluginにも問題なくアクセスできる。
```
http://localhost:15672
```
