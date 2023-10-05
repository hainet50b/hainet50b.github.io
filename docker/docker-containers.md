# よく使うDockerコンテナ
{:.no_toc}

* toc
{:toc}

## Debian

[Docker Hub](https://hub.docker.com/_/debian){:target="_blank"} 🐳
```shell
docker run --name debian -it --rm debian /bin/bash
```

## Apache

[Docker Hub](https://hub.docker.com/_/httpd){:target="_blank"} 🐳
```shell
docker run --name apache -d -p 80:80 httpd
```
```shell
curl localhost:80
```

## Nginx

[Docker Hub](https://hub.docker.com/_/nginx){:target="_blank"} 🐳
```shell
docker run --name nginx -d -p 80:80 nginx
```
```shell
curl localhost:80
```

## Redis

[Docker Hub](https://hub.docker.com/_/redis){:target="_blank"} 🐳
```shell
docker run --name redis -d -p 6379:6379 redis
```
```shell
redis-cli
```
