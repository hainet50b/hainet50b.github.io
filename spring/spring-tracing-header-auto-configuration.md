# Spring AutoConfigurationによるトレースヘッダーの付与
サンプルリポジトリ：[spring-tracing-header-auto-configuration \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-web-gym/spring-tracing-header-auto-configuration){:target="_blank"}

## 依存関係
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-otel</artifactId>
</dependency>
```

## アプリケーション設定
```yml
management:
  tracing:
    enabled: true
    sampling:
      probability: 0
    propagation:
      type: w3c
```

## Bean定義
AutoConfigurationで生成されたBuilderを使用しないとトレースヘッダーが付与されない。

```java
@Bean
public RestTemplate autoConfiguredRestTemplate(RestTemplateBuilder builder) {
    return builder
            .rootUri("http://localhost:8080")
            .build();
}

@Bean
public RestTemplate selfConfiguredRestTemplate() {
    return new RestTemplateBuilder()
            .rootUri("http://localhost:8080")
            .build();
}

@Bean
public WebClient autoConfiguredWebClient(WebClient.Builder builder) {
    return builder
            .baseUrl("http://localhost:8080")
            .build();
}

@Bean
public WebClient selfConfiguredWebClient() {
    return WebClient.builder()
            .baseUrl("http://localhost:8080")
            .build();
}
```

## トレースヘッダー確認
HTTPヘッダーをダンプするエンドポイントを作成する。
```java
@PostMapping("/")
public void printHeaders(@RequestHeader HttpHeaders headers) {
    headers.forEach((k, v) -> System.out.printf("%s: %s\n", k, v));
}
```

作成したエンドポイントにRestTemplate/WebClientで通信する。
```java
@Bean
public CommandLineRunner useAutoConfiguredRestTemplate(RestTemplate autoConfiguredRestTemplate) {
    return args -> {
        System.out.println("[AutoConfiguredRestTemplate]");
        autoConfiguredRestTemplate.postForEntity("/", null, String.class);
    };
}

@Bean
public CommandLineRunner useSelfConfiguredRestTemplate(RestTemplate selfConfiguredRestTemplate) {
    return args -> {
        System.out.println("[SelfConfiguredRestTemplate]");
        selfConfiguredRestTemplate.postForEntity("/", null, String.class);
    };
}

@Bean
public CommandLineRunner useAutoConfiguredWebClient(WebClient autoConfiguredWebClient) {
    return args -> {
        System.out.println("[AutoConfiguredWebClient]");
        autoConfiguredWebClient
                .post()
                .uri("/")
                .retrieve()
                .bodyToMono(String.class)
                .block();
    };
}

@Bean
public CommandLineRunner useSelfConfiguredWebClient(WebClient selfConfiguredWebClient) {
    return args -> {
        System.out.println("[SelfConfiguredWebClient]");
        selfConfiguredWebClient
                .post()
                .uri("/")
                .retrieve()
                .bodyToMono(String.class)
                .block();
    };
}
```

各RestTemplate/WebClientごとに以下のログが出力される。
```
[AutoConfiguredRestTemplate]
traceparent: [00-fe1b17de394e001c790e028f76101024-1db3bc4fcf7b52ad-00]
accept: [text/plain, application/json, application/*+json, */*]
user-agent: [Java/17.0.8.1]
host: [localhost:8080]
connection: [keep-alive]
content-type: [application/x-www-form-urlencoded]
content-length: [0]

[SelfConfiguredRestTemplate]
accept: [text/plain, application/json, application/*+json, */*]
user-agent: [Java/17.0.8.1]
host: [localhost:8080]
connection: [keep-alive]
content-type: [application/x-www-form-urlencoded]
content-length: [0]

[AutoConfiguredWebClient]
accept-encoding: [gzip]
user-agent: [ReactorNetty/1.1.11]
host: [localhost:8080]
accept: [*/*]
transfer-encoding: [chunked]
traceparent: [00-5df911cf5ab0717845820b0acfb18b40-07d64fce586ac7b9-00]

[SelfConfiguredWebClient]
accept-encoding: [gzip]
user-agent: [ReactorNetty/1.1.11]
host: [localhost:8080]
accept: [*/*]
transfer-encoding: [chunked]
```
