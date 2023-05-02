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
