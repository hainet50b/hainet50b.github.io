# Keytoolによる擬似CA証明書とサーバー証明書の作成

![java-keytool-9b5915d8fc17.png](https://programacho.blob.core.windows.net/images/java-keytool-9b5915d8fc17.png)

### ①CA証明書を作成する
```shell
keytool -genkeypair \
  -alias ca \
  -keyalg RSA \
  -keysize 2048 \
  -validity 3650 \
  -keystore ca.p12 \
  -storetype pkcs12 \
  -storepass password \
  -keypass password
```

### ②CA証明書をエクスポートする
```shell
keytool -exportcert \
  -alias ca \
  -file ca.crt \
  -keystore ca.p12 \
  -storetype pkcs12 \
  -storepass password \
  -rfc
```

### オプション：証明書の確認
```shell
openssl x509 -in ca.crt -text -noout
```

### ③CA証明書の秘密鍵をエクスポートする
```shell
openssl pkcs12 \
  -in ca.p12 \
  -out ca.key \
  -nocerts \
  -nodes \
  -passin pass:password
```

### ④サーバー証明書を作成する
```shell
keytool -genkeypair \
  -alias server \
  -keyalg RSA \
  -keysize 2048 \
  -validity 3650 \
  -keystore server.p12 \
  -storetype pkcs12 \
  -storepass password \
  -keypass password
```

### ⑤サーバー証明書の署名リクエストを作成する
```shell
keytool -certreq \
  -alias server \
  -file server.csr \
  -keystore server.p12 \
  -storetype pkcs12 \
  -storepass password
```

### ⑥CA証明書でサーバー証明書を署名する
事前に以下のファイルを`server_cert.conf`という名称で作成しておく。
```conf
[ v3_ca ]
basicConstraints = CA:FALSE
```

```shell
openssl x509 -req \
  -in server.csr \
  -CA ca.crt \
  -CAkey ca.key \
  -out server.crt \
  -CAcreateserial \
  -days 3650 \
  -sha256 \
  -extfile server_cert.conf \
  -extensions v3_ca
```

### オプション：CA証明書をインポートする
サーバー証明書をインポートするにあたって事前に擬似CA証明書を信頼するよう設定する。

```shell
keytool -importcert \
  -trustcacerts \
  -alias ca \
  -file ca.crt \
  -keystore server.p12 \
  -storetype pkcs12 \
  -storepass password
```

### ⑦署名したサーバー証明書をインポートする
```shell
keytool -importcert \
  -alias server \
  -file server.crt \
  -keystore server.p12 \
  -storetype pkcs12 \
  -storepass password
```

### サーバー証明書を検証する
```shell
openssl verify -CAfile ca.crt server.crt
```
