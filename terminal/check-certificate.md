# opensslコマンドによるサーバー証明書確認

## 証明書チェーンを取得
```shell
openssl s_client -connect pages.programacho.com:443 -showcerts
```

取得した証明書チェーンは以下の方法で整形できる。
```shell
-- サーバー証明書のみ出力
openssl s_client -connect pages.programacho.com:443 -showcerts \
| openssl x509 -text

-- サーバー証明書の有効期限のみ出力
openssl s_client -connect pages.programacho.com:443 -showcerts \
| openssl x509 -dates -noout
```
