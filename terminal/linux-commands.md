# よく使うLinuxコマンド

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
## SHAR256
echo hainet50b | shasum
```
