# Spring Web HTTPSサーバーの構築
{:.no_toc}

* toc
{:toc}

## オレオレ証明書の作成
オレオレ証明書を作成してSpring Webアプリケーションのsrc/main/resourcesに配置する。

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

姓名は何ですか。
  [Unknown]:  programacho.com
組織単位名は何ですか。
  [Unknown]:
組織名は何ですか。
  [Unknown]:  programacho.com
都市名または地域名は何ですか。
  [Unknown]:  Tokyo
都道府県名または州名は何ですか。
  [Unknown]:  Tokyo
この単位に該当する2文字の国コードは何ですか。
  [Unknown]:  JP
CN=programacho.com, OU=Unknown, O=programacho.com, L=Tokyo, ST=Tokyo, C=JPでよろしいですか。
  [いいえ]:  Y

3,650日間有効な2,048ビットのRSAのキー・ペアと自己署名型証明書(SHA256withRSA)を生成しています
  ディレクトリ名: CN=programacho.com, OU=Unknown, O=programacho.com, L=Tokyo, ST=Tokyo, C=JP
```

## キーストアの指定
application.ymlでキーストアを指定する。

```yml
server:
  port: 8443
  ssl:
    key-store-type: PKCS12
    key-store: classpath:server.p12
    key-store-password: password
    key-alias: server
```

## 動作確認
アプリケーションを起動してcurlでアクセスすると以下のようになる。

```shell
# HTTPではアクセスできない。
curl localhost:8443/
Bad Request
This combination of host and port requires TLS.

# HTTPSでもオレオレ証明書のため証明書の検証に失敗する。
curl https://localhost:8443/
curl: (60) SSL certificate problem: self signed certificate
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.

# 証明書の検証を無視することで正常にアクセスできる。
curl -k https://localhost:8443/
foo
```

またブラウザでアクセスすると作成したオレオレ証明書を確認できる。
![spring-web-https-server-f5a649eacf66.png](https://programacho.blob.core.windows.net/images/spring-web-https-server-f5a649eacf66wi-fi-router-7714d08326d8.png)
