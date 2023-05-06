# Docker Bits and Pieces

## OSの立ち上げ時にコンテナを自動起動する
参考：[コンテナを自動的に開始 | Docker-docs-ja](https://docs.docker.jp/config/container/start-containers-automatically.html){:target="_blank"

業務ではプラットフォームが整っていることもあるが、ちょっとしたツール環境で役に立つ。

```shell
# --restartオプションにはno/on-failure/always/unless-stoppedがある。
docker run --name mysql -d --restart always -p 3306:3306 -e MYSQL_ROOT_PASSWORD=changeme mysql:8.0.32
```