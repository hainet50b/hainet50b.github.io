# Spring Web HTTP Interface
サンプルリポジトリ：[spring-web-http-interface | GitHub](https://github.com/hainet50b/spring-web-gym/tree/main/spring-web-http-interface){:target="_blank"}

## 依存関係
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- WebClientに依存するためアーキテクチャがWeb MVCでもwebfluxを依存に加える -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

## インターフェースの宣言
```java
@HttpExchange("/users")
public interface UserClient {

    // Userはrecordを使用可能
    @GetExchange("/")
    List<User> findAll();

    @GetExchange("/{id}")
    User findById(@PathVariable String id);

    @PostExchange("/")
    User save(@RequestBody User user);
}
```

## DIコンテナへの登録
```java
@Configuration
public class WebClientConfig {

    @Bean
    public HttpServiceProxyFactory httpServiceProxyFactory(WebClient.Builder builder) {
        WebClient webClient = builder
                .baseUrl("http://localhost:8080")
                .build();

        return HttpServiceProxyFactory.builder(WebClientAdapter.forClient(webClient)).build();
    }

    // WebClientとHTTP Interfaceは1対nの関係性になるためBean定義を分ける
    @Bean
    public UserClient userClient(HttpServiceProxyFactory factory) {
        return factory.createClient(UserClient.class);
    }
}
```

## タイムアウト設定
```java
@Bean
public HttpServiceProxyFactory httpServiceProxyFactory(WebClient.Builder builder) {
    HttpClient httpClient = HttpClient.create()
            // Connect Timeout
            .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 2_000)
            // Read Timeout
            .doOnConnected(conn -> conn
                    .addHandlerLast(new ReadTimeoutHandler(3_000, TimeUnit.MILLISECONDS))
            );

    WebClient webClient = builder
            .clientConnector(new ReactorClientHttpConnector(httpClient))
            .build();

    return HttpServiceProxyFactory
            .builder(WebClientAdapter.forClient(webClient))
            // Mono/Fluxおけるブロッキング処理のタイムアウト設定
            .blockTimeout(Duration.ofMillis(5_000))
            .build();
}
```

## 例外ハンドリング
```java
try {
    userClient.findAll();
} catch (WebClientRequestException e) {
    // 起因例外によってConnect/Read Timeoutを判別可能
    if (e.getCause() instanceof ConnectTimeoutException) {
        // Connect Timeout
    } else if (e.getCause() instanceof ReadTimeoutException) {
        // Read Timeout
    } else {
        // その他の通信に関する問題
    }
} catch (WebClientResponseException e) {
    // HTTPステータスコードによる判別も可能
    if (e.getStatusCode().is4xxClientError()) {
        // 400系のHTTPステータスコード
    } else if (e.getStatusCode().is5xxServerError()) {
        // 500系のHTTPステータスコード
    } else {
        // その他のレスポンスに関する問題
    }
} catch (RuntimeException e) {
    // その他の問題
}
```