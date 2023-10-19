# Java HTTPクライアント 証明書検証の無効化
{:.no_toc}

* toc
{:toc}

## URLConnection
URLConnectionで通信する前に以下の設定を実行しておく。

```java
X509TrustManager[] trustManager = {
    new X509TrustManager() {
        @Override
        public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
        }

        @Override
        public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
        }

        @Override
        public X509Certificate[] getAcceptedIssuers() {
            return null;
        }
    }
};

SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(null, trustManager, null);

HttpsURLConnection.setDefaultSSLSocketFactory(sslContext.getSocketFactory()); // 証明書検証無効化
HttpsURLConnection.setDefaultHostnameVerifier((str, sslSession) -> true); // ホスト名検証無効化
```

サーバーがオレオレ証明書を採用していても問題なく通信できる。

```java
URL url = new URL("https://localhost:8443/");
URLConnection connection = url.openConnection();
BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream()));

String line;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}

br.close();
```

「証明書検証無効化」設定をコメントアウトすると以下の例外が発生する。

```
Caused by: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:378) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:321) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:316) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1357) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.onConsumeCertificate(CertificateMessage.java:1232) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.consume(CertificateMessage.java:1175) ~[na:na]
    at java.base/sun.security.ssl.SSLHandshake.consume(SSLHandshake.java:396) ~[na:na]
    at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:480) ~[na:na]
    at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:458) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.dispatch(TransportContext.java:201) ~[na:na]
    at java.base/sun.security.ssl.SSLTransport.decode(SSLTransport.java:172) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1510) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1425) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:455) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:426) ~[na:na]
    at java.base/sun.net.www.protocol.https.HttpsClient.afterConnect(HttpsClient.java:589) ~[na:na]
    at java.base/sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(AbstractDelegateHttpsURLConnection.java:187) ~[na:na]
    at java.base/sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1665) ~[na:na]
    at java.base/sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1589) ~[na:na]
    at java.base/sun.net.www.protocol.https.HttpsURLConnectionImpl.getInputStream(HttpsURLConnectionImpl.java:224) ~[na:na]
    at com.programacho.javahttpclientskipcertvalidation.JavaHttpClientSkipCertValidationApplication.lambda$run$1(JavaHttpClientSkipCertValidationApplication.java:61) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:439) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:306) ~[na:na]
    at java.base/sun.security.validator.Validator.validate(Validator.java:264) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:231) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:132) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1341) ~[na:na]
    ... 23 common frames omitted
Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.build(SunCertPathBuilder.java:146) ~[na:na]
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.engineBuild(SunCertPathBuilder.java:127) ~[na:na]
    at java.base/java.security.cert.CertPathBuilder.build(CertPathBuilder.java:297) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:434) ~[na:na]
    ... 28 common frames omitted
```

「ホスト名検証無効化」設定をコメントアウトすると以下の例外を発生する。

```
Caused by: javax.net.ssl.SSLHandshakeException: No name matching localhost found
    at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:378) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:321) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:316) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1357) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.onConsumeCertificate(CertificateMessage.java:1232) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.consume(CertificateMessage.java:1175) ~[na:na]
    at java.base/sun.security.ssl.SSLHandshake.consume(SSLHandshake.java:396) ~[na:na]
    at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:480) ~[na:na]
    at java.base/sun.security.ssl.HandshakeContext.dispatch(HandshakeContext.java:458) ~[na:na]
    at java.base/sun.security.ssl.TransportContext.dispatch(TransportContext.java:201) ~[na:na]
    at java.base/sun.security.ssl.SSLTransport.decode(SSLTransport.java:172) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.decode(SSLSocketImpl.java:1510) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.readHandshakeRecord(SSLSocketImpl.java:1425) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:455) ~[na:na]
    at java.base/sun.security.ssl.SSLSocketImpl.startHandshake(SSLSocketImpl.java:426) ~[na:na]
    at java.base/sun.net.www.protocol.https.HttpsClient.afterConnect(HttpsClient.java:589) ~[na:na]
    at java.base/sun.net.www.protocol.https.AbstractDelegateHttpsURLConnection.connect(AbstractDelegateHttpsURLConnection.java:187) ~[na:na]
    at java.base/sun.net.www.protocol.http.HttpURLConnection.getInputStream0(HttpURLConnection.java:1665) ~[na:na]
    at java.base/sun.net.www.protocol.http.HttpURLConnection.getInputStream(HttpURLConnection.java:1589) ~[na:na]
    at java.base/sun.net.www.protocol.https.HttpsURLConnectionImpl.getInputStream(HttpsURLConnectionImpl.java:224) ~[na:na]
    at com.programacho.javahttpclientskipcertvalidation.JavaHttpClientSkipCertValidationApplication.lambda$run$0(JavaHttpClientSkipCertValidationApplication.java:61) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
Caused by: java.security.cert.CertificateException: No name matching localhost found
    at java.base/sun.security.util.HostnameChecker.matchDNS(HostnameChecker.java:234) ~[na:na]
    at java.base/sun.security.util.HostnameChecker.match(HostnameChecker.java:103) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:458) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:432) ~[na:na]
    at java.base/sun.security.ssl.AbstractTrustManagerWrapper.checkAdditionalTrust(SSLContextImpl.java:1475) ~[na:na]
    at java.base/sun.security.ssl.AbstractTrustManagerWrapper.checkServerTrusted(SSLContextImpl.java:1442) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1341) ~[na:na]
    ... 23 common frames omitted
```
