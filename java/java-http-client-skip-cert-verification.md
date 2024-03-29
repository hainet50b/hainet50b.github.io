# Java HTTPクライアント 証明書検証の無効化
{:.no_toc}

* toc
{:toc}

## URLConnection
URLConnectionで通信する前に以下の設定を実行しておく。

```java
X509TrustManager[] trustManagers = {
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
sslContext.init(null, trustManagers, null);

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
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$1(JavaHttpClientSkipCertVerificationApplication.java:61) ~[classes/:na]
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

「ホスト名検証無効化」設定をコメントアウトすると以下の例外が発生する。

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
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$0(JavaHttpClientSkipCertVerificationApplication.java:61) ~[classes/:na]
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

## Apache HttpClient
Apache HttpClientの依存を追加する。

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.14</version>
</dependency>
```

Apache HttpClientで通信する前に以下の設定を実行しておく。

```java
SSLContext sslContext = SSLContexts.custom()
        .loadTrustMaterial(new TrustAllStrategy()) // 証明書検証無効化
        .build();

SSLConnectionSocketFactory connectionSocketFactory = new SSLConnectionSocketFactory(
        sslContext,
        NoopHostnameVerifier.INSTANCE // ホスト名検証無効化
);

Registry<ConnectionSocketFactory> registry = RegistryBuilder.<ConnectionSocketFactory>create()
        .register("https", connectionSocketFactory)
        .build();

PoolingHttpClientConnectionManager httpClientConnectionManager = new PoolingHttpClientConnectionManager(registry);
// HTTPコネクションプールなどの設定を実施する。

return HttpClients.custom()
        .setConnectionManager(httpClientConnectionManager)
        .build();
```

サーバーがオレオレ証明書を採用していても問題なく通信できる。

```java
CloseableHttpClient httpClient = HttpClients.custom()
        .setConnectionManager(httpClientConnectionManager)
        .build();

CloseableHttpResponse response = httpClient.execute(new HttpGet("https://localhost:8443/"));
System.out.println(EntityUtils.toString(response.getEntity()));
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
    at org.apache.http.conn.ssl.SSLConnectionSocketFactory.createLayeredSocket(SSLConnectionSocketFactory.java:436) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.conn.ssl.SSLConnectionSocketFactory.connectSocket(SSLConnectionSocketFactory.java:384) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.conn.DefaultHttpClientConnectionOperator.connect(DefaultHttpClientConnectionOperator.java:142) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:376) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.MainClientExec.establishRoute(MainClientExec.java:393) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:236) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:186) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.RetryExec.execute(RetryExec.java:89) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.RedirectExec.execute(RedirectExec.java:110) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:185) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:108) ~[httpclient-4.5.14.jar:4.5.14]
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$0(JavaHttpClientSkipCertVerificationApplication.java:69) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:439) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:306) ~[na:na]
    at java.base/sun.security.validator.Validator.validate(Validator.java:264) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:231) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:132) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1341) ~[na:na]
    ... 30 common frames omitted
Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.build(SunCertPathBuilder.java:146) ~[na:na]
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.engineBuild(SunCertPathBuilder.java:127) ~[na:na]
    at java.base/java.security.cert.CertPathBuilder.build(CertPathBuilder.java:297) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:434) ~[na:na]
    ... 35 common frames omitted
```

「ホスト名検証無効化」の`NoopHostnameVerifier.INSTANCE`を`SSLConnectionSocketFactory.getDefaultHostnameVerifier()`に変更すると以下の例外が発生する。

```
Caused by: javax.net.ssl.SSLPeerUnverifiedException: Certificate for <localhost> doesn't match any of the subject alternative names: []
    at org.apache.http.conn.ssl.SSLConnectionSocketFactory.verifyHostname(SSLConnectionSocketFactory.java:507) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.conn.ssl.SSLConnectionSocketFactory.createLayeredSocket(SSLConnectionSocketFactory.java:437) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.conn.ssl.SSLConnectionSocketFactory.connectSocket(SSLConnectionSocketFactory.java:384) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.conn.DefaultHttpClientConnectionOperator.connect(DefaultHttpClientConnectionOperator.java:142) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:376) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.MainClientExec.establishRoute(MainClientExec.java:393) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:236) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:186) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.RetryExec.execute(RetryExec.java:89) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.execchain.RedirectExec.execute(RedirectExec.java:110) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:185) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83) ~[httpclient-4.5.14.jar:4.5.14]
    at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:108) ~[httpclient-4.5.14.jar:4.5.14]
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$0(JavaHttpClientSkipCertVerificationApplication.java:70) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
```

## OkHttp
OkHttpの依存を追加する。

```xml
<dependency>
    <groupId>com.squareup.okhttp3</groupId>
    <artifactId>okhttp</artifactId>
    <version>4.12.0</version>
</dependency>
```

OkHttpで通信する前に以下の設定を実行しておく。

```java
X509TrustManager[] trustManagers = {
        new X509TrustManager() {
            @Override
            public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
            }

            @Override
            public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
            }

            @Override
            public X509Certificate[] getAcceptedIssuers() {
                return new X509Certificate[0];
            }
        }
};

SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(null, trustManagers, null);

return new OkHttpClient.Builder()
        .sslSocketFactory(
                sslContext.getSocketFactory(), 
                trustManagers[0]
        ) // 証明書検証無効化
        .hostnameVerifier((str, sslSession) -> true) // ホスト名検証無効化
        .build();
```

サーバーが俺俺証明書を採用していなくても問題なく通信できる。

```java
OkHttpClient httpClient = new OkHttpClient.Builder()
        .sslSocketFactory(
                sslContext.getSocketFactory(),
                trustManagers[0]
        )
        .hostnameVerifier((str, sslSession) -> true)
        .build();

Request request = new Request.Builder()
        .url("https://localhost:8443/")
        .build();

try (Response response = httpClient.newCall(request).execute()) {
    System.out.println(response.body().string());
}
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
    at okhttp3.internal.connection.RealConnection.connectTls(RealConnection.kt:379) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealConnection.establishProtocol(RealConnection.kt:337) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealConnection.connect(RealConnection.kt:209) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.findConnection(ExchangeFinder.kt:226) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.findHealthyConnection(ExchangeFinder.kt:106) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.find(ExchangeFinder.kt:74) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.initExchange$okhttp(RealCall.kt:255) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ConnectInterceptor.intercept(ConnectInterceptor.kt:32) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.cache.CacheInterceptor.intercept(CacheInterceptor.kt:95) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.BridgeInterceptor.intercept(BridgeInterceptor.kt:83) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RetryAndFollowUpInterceptor.intercept(RetryAndFollowUpInterceptor.kt:76) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.getResponseWithInterceptorChain$okhttp(RealCall.kt:201) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.execute(RealCall.kt:154) ~[okhttp-4.12.0.jar:na]
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$1(JavaHttpClientSkipCertVerificationApplication.java:66) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
Caused by: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:439) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.engineValidate(PKIXValidator.java:306) ~[na:na]
    at java.base/sun.security.validator.Validator.validate(Validator.java:264) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:231) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:132) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T13CertificateConsumer.checkServerCerts(CertificateMessage.java:1341) ~[na:na]
    ... 35 common frames omitted
Caused by: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.build(SunCertPathBuilder.java:146) ~[na:na]
    at java.base/sun.security.provider.certpath.SunCertPathBuilder.engineBuild(SunCertPathBuilder.java:127) ~[na:na]
    at java.base/java.security.cert.CertPathBuilder.build(CertPathBuilder.java:297) ~[na:na]
    at java.base/sun.security.validator.PKIXValidator.doBuild(PKIXValidator.java:434) ~[na:na]
    ... 40 common frames omitted
```

「ホスト名検証無効化」設定をコメントアウトすると以下の例外が発生する。

```
Caused by: javax.net.ssl.SSLPeerUnverifiedException: Hostname localhost not verified:
    certificate: sha256/tLKK3BpFijp8UcM0SS6wYlQazeUh0ipHvSW9Cl6DYH4=
    DN: CN=programacho.com, OU=Unknown, O=programacho.com, L=Tokyo, ST=Tokyo, C=JP
    subjectAltNames: []
    at okhttp3.internal.connection.RealConnection.connectTls(RealConnection.kt:389) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealConnection.establishProtocol(RealConnection.kt:337) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealConnection.connect(RealConnection.kt:209) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.findConnection(ExchangeFinder.kt:226) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.findHealthyConnection(ExchangeFinder.kt:106) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ExchangeFinder.find(ExchangeFinder.kt:74) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.initExchange$okhttp(RealCall.kt:255) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.ConnectInterceptor.intercept(ConnectInterceptor.kt:32) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.cache.CacheInterceptor.intercept(CacheInterceptor.kt:95) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.BridgeInterceptor.intercept(BridgeInterceptor.kt:83) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RetryAndFollowUpInterceptor.intercept(RetryAndFollowUpInterceptor.kt:76) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.http.RealInterceptorChain.proceed(RealInterceptorChain.kt:109) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.getResponseWithInterceptorChain$okhttp(RealCall.kt:201) ~[okhttp-4.12.0.jar:na]
    at okhttp3.internal.connection.RealCall.execute(RealCall.kt:154) ~[okhttp-4.12.0.jar:na]
    at com.programacho.javahttpclientskipcertverification.JavaHttpClientSkipCertVerificationApplication.lambda$run$0(JavaHttpClientSkipCertVerificationApplication.java:66) ~[classes/:na]
    at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:774) ~[spring-boot-3.1.4.jar:3.1.4]
    ... 5 common frames omitted
```
