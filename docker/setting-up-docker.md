# Docker環境の構築

## Mac
HomebrewでDockerデーモン／Docker CLI／Docker Desktop全てのインストールが完了する。
```shell
brew install docker
```

Apple Silicon搭載のMacでは一部のコンテナが動かないことがある。  
Colimaでアーキテクチャを指定してDockerデーモンを立てることで回避できる。
```shell
brew install colima

# アーキテクチャの他にもvCPUやメモリの割り当てを設定できる。
colima start --arch x86_64
```

Colimaは既存のDockerデーモンと共存できる。
```shell
# Dockerコンテキストを切り替えて使い分ける。
docker context use colima

colima
Current context is now "colima"

docker context list
NAME                TYPE                DESCRIPTION                               DOCKER ENDPOINT                                       KUBERNETES ENDPOINT   ORCHESTRATOR
colima *            moby                colima                                    unix:///Users/hainet50b/.colima/default/docker.sock
default             moby                Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                                                 swarm
desktop-linux       moby                                                          unix:///Users/hainet50b/.docker/run/docker.sock
```

## Windows
Windows環境ではScoopなどのパッケージマネージャーを使用して上手くインストールする手段が見つからなかった。  
[公式ページ](https://www.docker.com/products/docker-desktop/)からDocker Desktopのインストーラーをダウンロードしてインストールする。

WindowsのWSL環境でDockerデーモンが上手く立ち上がらないことがある。  
この時は以下のコマンドでWSLを再起動することで解決することが多い。
```PowerShell
Restart-Service vmcompute
```
