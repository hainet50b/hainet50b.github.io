# よく使うLinuxコマンド
{:.no_toc}

* toc
{:toc}

## SSHログイン
```shell
# ユーザー名を指定してログイン
ssh 192.0.2.1 -l hainet50b

# 秘密鍵を指定してログイン
ssh 192.0.2.1 -l hainet50b -i ~/.ssh/id_ed25519

# ポート番号を指定してログイン
ssh 192.0.2.1 -p 122 -l hainet50b
```

## リモートマシンにファイル転送（SCP）
```shell
# ローカルファイルをリモートマシンに転送
# 以下のコマンドを発行するとpmacho_userのパスワードを聞かれる。
scp /path/to/local_file pmacho_user@pmacho_host:/path/to/remote_directory

# リモートファイルをローカルマシンに転送
scp pmacho_user@pmacho_host:/path/to/remote_directory /path/to/local_file
```

| オプション | 概要 | サンプル |
| --- | --- | --- |
| -P | ポート番号を指定して転送 | scp -P 122 ... |
| -i | 秘密鍵を指定して転送 | scp -i ~/.ssh/id_ed25519 ... |
| -p | ディレクトリを再起的に転送 | scp -p ... |

## telnetでTCPコネクションの確立を確認する
```shell
telnet pages.programacho.com 443
Trying 185.199.110.153...
Connected to hainet50b.github.io.
```

## tar.gzファイルを展開／作成する
```shell
# アーカイブを展開
tar -xvf programacho.tar.gz

# アーカイブを作成
tar -cvf programacho.tar.gz /path/to/programacho
```

| オプション | 概要 |
| --- | --- |
| -x (--extract) | アーカイブを展開する |
| -v (--verbose) | 処理するファイルを標準出力に表示する |
| -f (--file) | 処理するアーカイブを指定する |
| -c (--create) | アーカイブを作成する |

## シンボリックリンクを作成する
```shell
ln -s /path/to/target /path/to/link

ls -la /path/to/link
lrwxr-xr-x@ 1 hainet50b staff 5 Jun 21 23:16 /path/to/link -> /path/to/target

readlink /path/to/link
/path/to/target
```

## エポック秒を取得する
```shell
date +%s

# 現地時刻に変換する
# Linux (GNU版 dateコマンド)
date -d @1689410115
# Mac (BSD版 dateコマンド)
date -r 1689410115
```

## Base64エンコード／デコード
```shell
# エンコード
echo programacho | base64
cHJvZ3JhbWFjaG8K

# デコード
echo cHJvZ3JhbWFjaG8K | base64 -d
programacho
```

## ハッシュ
```shell
# Linux
## MD5
echo hainet50b | md5sum
## SHA256
echo hainet50b | sha256sum

# Mac
## MD5
echo hainet50b | md5
## SHA256
echo hainet50b | shasum

# Windows PowerShell (TODO: ハッシュ関連事項のページ分離)
## MD5
Get-FileHash -Algorithm MD5 .\pmacho.txt
## SHA256
Get-FileHash -Algorithm SHA256 .\pmacho.txt

# Windows コマンドプロンプト
## MD5
certutil -hashfile pmacho.txt MD5
## SHA256
certutil -hashfile pmacho.txt SHA256
```

## ファイルを行数を指定して分割
```shell
# 50行のファイルを用意する
wc -l pmacho.txt
50 pmacho.txt

# 10行ずつに分割する
split -l 10 pmacho.txt pmacho-

ls -la
total 48
-rw-r--r--@  1 hainet50b  staff   21 Sep 12 19:47 pmacho-aa
-rw-r--r--@  1 hainet50b  staff   30 Sep 12 19:47 pmacho-ab
-rw-r--r--@  1 hainet50b  staff   30 Sep 12 19:47 pmacho-ac
-rw-r--r--@  1 hainet50b  staff   30 Sep 12 19:47 pmacho-ad
-rw-r--r--@  1 hainet50b  staff   30 Sep 12 19:47 pmacho-ae
-rw-r--r--@  1 hainet50b  staff  141 Sep 12 19:43 pmacho.txt

tail -n 2 pmacho-aa
9
10

tail -n 2 pmacho-ae
49
50
```

## アプリケーションのTCPコネクション確立状況を確認
データベースコネクションなどを確認できる。

```shell
lsof -i -n -P | grep 3306

TODO
```

Windowsの場合は以下のようにして同様のことを確認できる。

```powershell
# MySQL 3306がLISTENINGで10本のTCPコネクションの確立を確認できう。
netstat -ano | grep 3306
  TCP         0.0.0.0:3306           0.0.0.0:0              LISTENING       13808
  TCP         127.0.0.1:3306         127.0.0.1:61677        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61678        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61679        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61680        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61681        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61682        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61683        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61684        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61685        ESTABLISHED     13808
  TCP         127.0.0.1:3306         127.0.0.1:61686        ESTABLISHED     13808
  TCP         127.0.0.1:61677        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61678        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61679        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61680        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61681        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61682        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61683        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61684        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61685        127.0.0.1:3306         ESTABLISHED     21180
  TCP         127.0.0.1:61686        127.0.0.1:3306         ESTABLISHED     21180
```

## コマンドをバックグランドで実行（nohup + &）
```shell
# nohup ターミナルを閉じてもプロセスを終了しない
# & コマンドをバックグラウンドで実行する
nohup java -jar pmacho.jar &
```