# Spring Boot 3.2
{:.no_toc}

* toc
{:toc}

リリースノート  
[Spring Boot 3.2 Release Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-3.2-Release-Notes){:target="_blank"}

## ログにアプリケーション名を出力
`spring.application.name`の値が自動的にログに含まれるようになった。

```
// spring.application.nameを指定していない場合
2023-11-28T17:26:03.041+09:00  INFO 28299 --- [           main] c.p.s.SpringBoot32Application            : Be programacho!

// spring.application.nameを指定した場合
2023-11-28T17:26:43.178+09:00  INFO 28310 --- [pmacho-app] [           main] c.p.s.SpringBoot32Application            : Be programacho!
```

## デフォルトでトレースIDをログに出力
ドキュメント  
[Logging Correlation IDs](https://docs.spring.io/spring-boot/docs/3.2.0/reference/html//actuator.html#actuator.micrometer-tracing.logging){:target="_blank"}

`spring-boot-actuator`とTracerBridgeが依存に含まれていると自動的にログにトレースIDを出力するようになった。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-otel</artifactId>
</dependency>

<!-- micrometer-tracingだけではトレースIDの発番が機能しない -->
<!-- <dependency>-->
<!--     <groupId>io.micrometer</groupId>-->
<!--     <artifactId>micrometer-tracing</artifactId>-->
<!-- </dependency>-->
```

```
// TracerBridgeが依存にある場合
2023-11-28T17:51:07.667+09:00  INFO 30250 --- [pmacho-app] [nio-8080-exec-1] c.p.s.SpringBoot32Application            : Be programacho!

// TracerBridgeが依存にない場合
2023-11-28T17:51:50.070+09:00  INFO 30304 --- [pmacho-app] [nio-8080-exec-1] [067a6cae654873ea5a6520d1b54777c6-c43304df46d51902] c.p.s.SpringBoot32Application            : Be programacho!
```

MDCを出力すると、`traceId`と`spanId`が設定されていることが分かる。
```
{traceId=067a6cae654873ea5a6520d1b54777c6, spanId=c43304df46d51902}
```

## RestClientインターフェース
ドキュメント  
[RestClient](https://docs.spring.io/spring-boot/docs/3.2.0/reference/html//io.html#io.rest-client.restclient){:target="_blank"}

ノンブロッキングの`WebClient`に対応するブロッキングの`RestClient`が導入された。

`RestClient` Beanの生成はAutoConfigurationで設定された`RestClient.Builder`を使用する。
```java
@Bean
public RestClient restClient(RestClient.Builder builder) {
    return builder.build();
}
```

```java
restClient
        .get()
        .uri("https://pages.programacho.com/")
        .retrieve()
        // MonoでもFluxでもなくStringで取り扱っている。
        .body(String.class);
```

`RestTemplate`と同様にして各種の設定を行える。
```java
@Bean
public RestClient restClient(RestClient.Builder builder) {
        SimpleClientHttpRequestFactory requestFactory = new SimpleClientHttpRequestFactory();
        requestFactory.setConnectTimeout(Duration.ofMillis(100));
        requestFactory.setReadTimeout(Duration.ofMillis(3_000));

        return builder
                .baseUrl("https://pages.programacho.com")
                .requestFactory(requestFactory)
                .build();
}
```

## JdbcClientインターフェース
ドキュメント  
[Using JdbcClient](https://docs.spring.io/spring-boot/docs/3.2.0/reference/html//data.html#data.sql.jdbc-client){:target="_blank"}

`JdbcTemplate`がBean定義されていることを条件に`JdbcClient`もBean定義される。

```java
// 特にBean定義を行うことなく注入できる。
  public SpringBoot32Application(JdbcClient jdbcClient) {
      this.jdbcClient = jdbcClient;
  }

// NamedParameterJdbcTemplateと同様の記述を行える。
JdbcClient.ResultQuerySpec spec = jdbcClient
        .sql("SELECT * FROM items WHERE id = :id")
        .param("id", 1)
        .query();

spec.singleRow(); // Map<String, Object>
```

## Virtual Threadsのサポート
Java 21環境で`spring.threads.virtual.enabled`を`true`に設定するだけで有効にできる。  
Webスレッド数を絞った状態で、スリープ処理を実施するサンプルエンドポイントに対して並列リクエストを送信すると効果を実感しやすい。

```yml
spring:
  threads:
    virtual:
      enabled: true
```

## JVMのチェックポイント & リストア対応
ドキュメント  
[Checkpoint and Restore With the JVM](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#deployment.efficient.checkpoint-restore){:target="_blank"}

TODO
