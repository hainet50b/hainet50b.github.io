# Apache SkyWalkingのセットアップ

## Dockerで構築
```shell
docker network create oap-network
docker run --name oap -d --network oap-network apache/skywalking-oap-server:9.5.0
docker run --name oap-ui -d --network oap-network -p 8080:8080 -e SW_OAP_ADDRESS=http://oap:12800 apache/skywalking-ui
```
