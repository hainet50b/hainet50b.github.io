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
