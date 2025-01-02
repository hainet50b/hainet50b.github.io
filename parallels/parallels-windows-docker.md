# Windows仮想マシンへのDocker環境の構築
{:.no_toc}

* toc
{:toc}

## 背景

Parallels Desktop for MacではPro Edition以上でなければNested Virtualizationをサポートしていない。  
さらにApple Silicon搭載のMacではEditionにかかわらず該当機能を使用できない（2025年1月2日現在）。

これによりParallelsで構築したWindows仮想マシンではWSL2が使えず、Dockerも使えない。  
この問題に対応すべくParallelsでWindows仮想マシンと並列にLinux仮想マシンを立ち上げて、  
Linuxに構築したDockerデーモンにWindowsからSSHで接続する。

画像TODO（概要図）

## 概要

1. ParallelsでLinux仮想マシン（Ubuntu）を構築する
1. UbuntuにDocker環境を構築する
1. WindowsからUbuntuのDockerにTCPで接続する
1. （オプション）TCP接続からSSH接続に切り替える
1. （オプション）UbuntuのIPアドレスを固定する
1. （オプション）Windowsで名前解決をする
1. （オプション）SSHポートフォワードでlocalhostでアクセスする
1. （オプション）バインドマウント用にファイル共有を有効化する

## 手順

### ParallelsでLinux仮想マシンを構築する

ParallelsのインストールアシスタントでUbuntuを選択してインストールする。

![parallels-windows-docker-install-assistant.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-install-assistant.png)

インストール完了後にParallels Toolsのインストールウィザードが起動する。  
これをインストールしてMacとのクリップボード共有を有効にしておく。

パスワード入力時には英字配列のため記号の入力に気を付ける。
![parallels-windows-docker-ubuntu-password.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-ubuntu-password.png)

ネットワーク設定を「共有ネットワーク（推奨）」にしておくこと。
![parallels-windows-docker-shared-network.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-shared-network.png)

Macとのファイル共有を解除しておく（追って限定的に共有する）
![parallels-windows-docker-disable-mac-file-sharing.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-disable-mac-file-sharing.png)

Macとのアプリケーション共有を解除しておく。
![parallels-windows-docker-disable-mac-application-sharing.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-disable-mac-application-sharing.png)

Macとのプリンタ共有を解除しておく。
![parallels-windows-docker-disable-mac-printer-sharing.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-disable-mac-printer-sharing.png)

### UbuntuにDocker環境を構築する

UbuntuにDockerをインストールする。

```sh
sudo apt update
sudo apt install -y docker.io
```

Dockerがsystemdに登録されて起動していることを確認する。

```sh
sudo systemctl enable docker
systemctl status docker

sudo docker ps
```

### WindowsからUbuntuのDockerにTCPで接続する

UbuntuのDocker設定で外部接続を許可する。

```sh
sudo touch /etc/docker/daemon.json
sudo nano /etc/docker/daemon.json
```

daemon.json

```
{
    "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"]
}
```

dockerdの起動コマンドからfdソケットの設定を取り除く。

```sh
sudo nano /usr/lib/systemd/system/docker.service
```

docker.service

```
- ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
+ ExecStart=/usr/bin/dockerd --containerd=/run/containerd/containerd.sock
```

ユニットファイルを再読み込みしてから、Dockerを再起動する。

```sh
sudo systemctl daemon-reload
sudo systemctl restart docker

sudo docker ps
```

Windowsから接続するためにIPアドレスを確認する。

```
ip addr
```

```
2: enp0s5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:1c:42:06:73:04 brd ff:ff:ff:ff:ff:ff
    inet 10.211.55.5/24 metric 100 brd 10.211.55.255 scope global dynamic enp0s5
       valid_lft 1488sec preferred_lft 1488sec
...（省略）
```

WindowsでDockerコマンドの接続先を変更する。

```pwsh
$env:DOCKER_HOST = "tcp://10.211.55.5:2375"
```

PowerShellの起動スクリプトに設定しておくとよい。

```pwsh
echo '$env:DOCKER_HOST = "tcp://10.211.55.5:2375"' >> $PROFILE
```

WindowsからLinuxのDockerへ接続できることを確認する。

```pwsh
docker ps
```

### （オプション）TCP接続からSSH接続に切り替える

UbuntuにSSHサーバーをインストールする。

```sh
sudo apt install -y openssh-server
```

SSHサーバーがsystemdに登録されて起動していることを確認する。

```sh
sudo systemctl enable ssh
systemctl status ssh
```

Windowsの公開鍵をUbuntuに送信する。

```pwsh
scp $HOME\.ssh\id_ed25519.pub parallels@10.211.55.5:~/.ssh/
```

Ubuntuで送信した公開鍵を `authorized_keys` に追記する。

```sh
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
```

WindowsからUbuntuへSSH接続できることを確認する。

```pwsh
ssh parallels@10.211.55.5
```

Ubuntuの `parallels` ユーザーを `docker` グループに所属させる。

```sh
sudo usermod -aG docker parallels

groups parallels
parallels : parallels adm cdrom sudo dip plugdev lxd docker
```

WindowsでDockerコマンドの接続先を変更して、SSH接続できることを確認する。

```pwsh
$env:DOCKER_HOST = "ssh://parallels@10.211.55.5"

docker ps
```

PowerShellの起動スクリプトも変更する。

```pwsh
(Get-Content $PROFILE) -replace "tcp://10.211.55.5:2375", "ssh://parallels@10.211.55.5" | Set-Content $PROFILE

. $PROFILE
docker ps
```

### （オプション）UbuntuのIPアドレスを固定する

UbuntuのDHCPを無効化する。

```sh
sudo nano /etc/netplan/00-installer-config.yaml 
```

00-installer-config.yaml 

```yaml
network:
  ethernets:
    enp0s5:
      dhcp4: false
      addresses:
        - 10.211.55.5/24
      routes:
        - to: default
          via: 10.211.55.1
      nameservers:
        addresses:
          - 8.8.8.8
  version: 2
```

ネットワーク設定ファイルの権限を変更する。

```sh
sudo chmod 600 /etc/netplan/00-installer-config.yaml
```

ネットワーク設定を適用する。

```sh
sudo netplan apply
```

### （オプション）Windowsで名前解決をする

hostsファイルを編集して名前解決できるようにする。  
管理者権限で以下のコマンドを実行する。

```pwsh
echo "10.211.55.5 paralells" >> C:\Windows\System32\drivers\etc\hosts
```

Dockerコマンドの接続先も変更する。

```pwsh
$env:DOCKER_HOST = "ssh://parallels@parallels"

docker ps
```

PowerShellの起動スクリプトも変更する。

```pwsh
(Get-Content $PROFILE) -replace "ssh://parallels@10.211.55.5", "ssh://parallels@parallels" | Set-Content $PROFILE

. $PROFILE
docker ps
```

### （オプション）SSHポートフォワードでlocalhostを解決可能にする

サンプルアプリケーションを作成するとき各種アドレスにparallelsを含めたくない。  
そのためSSHポートフォワードでlocalhostを解決可能にする。

```pwsh
ssh -fN -L 80:localhost:80 parallels@parallels
```

参考にDockerでnginxを立ち上げてアクセスしてみる。

```pwsh
docker run -d -p 80:80 nginx

curl http://10.211.55.5/
<h1>Welcome to nginx!</h1>

curl http://parallels/
<h1>Welcome to nginx!</h1>

curl http://localhost/
<h1>Welcome to nginx!</h1>
```

### （オプション）バインドマウント用にファイル共有を有効化する

バインドマウント時にWindowsからUbuntuへファイル共有をするためにMacを経由する。  
WindowsとUbuntuそれぞれでMacとファイル共有を有効化することで疑似的に実現させる。

Macでファイル共有用のディレクトリを作成する。

```sh
mkdir $HOME/Develop/docker
```

WindowsにMacフォルダーを共有する。
![parallels-windows-docker-windows-file-sharing.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-windows-file-sharing.png)

Windowsからは以下のディレクトリにマウントされる。

```pwsh
ls \\Mac\docker
```

UbuntuにMacフォルダーを共有する
![parallels-windows-docker-ubuntu-file-sharing.png](https://programacho.blob.core.windows.net/images/parallels-windows-docker-ubuntu-file-sharing.png)

Ubuntuからは以下のディレクトリにマウントされる。

```sh
ls /media/psf/docker
```

例えばWindowsで静的ファイルを共有フォルダに配置して、  
Ubuntuで動作するDockerで起動したnginxから参照することができる。

```pwsh
mkdir \\Mac\docker\nginx\html
New-Item -Path \\Mac\docker\nginx\html\programacho.txt
echo "programacho.com" >> \\Mac\docker\nginx\html\programacho.txt

docker run --name nginx -d -p 80:80 -v /media/psf/docker/nginx/html:/usr/share/nginx/html:ro nginx
# 共有フォルダを使用するとnginxが上手く動作しないためsendfileを無効化する。
docker exec nginx sed -i 's/sendfile        on/sendfile        off/g' /etc/nginx/nginx.conf
docker exec nginx nginx -s reload

curl http://parallels/programacho.txt
programacho.com
```
