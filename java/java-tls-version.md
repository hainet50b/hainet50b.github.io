# TLSバージョンの確認
{:.no_toc}

* toc
{:toc}

## 接続先サーバーのTLS対応状況の確認
TLSバージョン単位に確認する。

```shell
# TLSv1.3（対応）
openssl s_client -connect pages.programacho.com:443 -tls1_3

New, TLSv1/SSLv3, Cipher is AEAD-CHACHA20-POLY1305-SHA256
SSL-Session:
    Protocol  : TLSv1.3

# TLSv1.2（対応）
openssl s_client -connect pages.programacho.com:443 -tls1_2

New, TLSv1/SSLv3, Cipher is ECDHE-RSA-CHACHA20-POLY1305
SSL-Session:
    Protocol  : TLSv1.2

# TLSv1.1（非対応）
openssl s_client -connect pages.programacho.com:443 -tls1_1

New, (NONE), Cipher is (NONE)
SSL-Session:
    Protocol  : TLSv1.1
```

## 接続元クライアント（Javaアプリケーション）のTLS対応状況の確認

```java
try (SSLSocket socket = (SSLSocket) SSLContext
        .getDefault()   
        .getSocketFactory()
        .createSocket()) {
    socket.getSupportedProtocols();
}

[TLSv1.3, TLSv1.2, TLSv1.1, TLSv1, SSLv3, SSLv2Hello]
```

## 実際にTLS接続を確立したセッションで採用されているバージョンの確認

```java
try (SSLSocket socket = (SSLSocket) SSLContext
        .getDefault()
        .getSocketFactory()
        .createSocket("pages.programacho.com", 443)) {
    socket.startHandshake();
    socket.getSession().getProtocol();
}

TLSv1.3
```

## 接続元クライアント（Javaアプリケーション）のTLSバージョン変更
JVMオプションに以下を加える。

```
-Djdk.tls.client.protocols=TLSv1.2
```

TLS対応状況はそのままに優先的に採用されるバージョンが変化する。
```java
socket.getSupportedProtocols();
[TLSv1.3, TLSv1.2, TLSv1.1, TLSv1, SSLv3, SSLv2Hello]

socket.getSession().getProtocol();
TLSv1.2 // TLSv1.3ではなくTLSv1.2が採用される
```

## TLSハンドシェイクの情報をログに出力
JVMオプションに以下を加えて実際に通信を実施する。  
稼働中のシステムのTLS対応状況を確認するにはこの方法が最も確実で手間がない。

```
-Djavax.net.debug=ssl,handshake
```

TLSハンドシェイクを経て、`Negotiated...`のログに採用されたTLSバージョンが出力される。

```
"ClientHello": {
  "client version"      : "TLSv1.2",
  "extensions"          : [
    "supported_versions (43)": {
      "versions": [TLSv1.3, TLSv1.2]
    },
    ...
  ],
  ...
}

"ServerHello": {
  "server version"      : "TLSv1.2",
  "extensions"          : [
    "supported_versions (43)": {
      "selected version": [TLSv1.3]
    },
    ...
  ],
  ...
}

Negotiated protocol version: TLSv1.3
```
