# Spring Cloud Stream Binder Rabbit入門
{:.no_toc}

* toc
{:toc}

サンプルリポジトリ：[spring-cloud-stream-binder-rabbit-intro \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-cloud-gym/spring-cloud-stream-gym/spring-cloud-stream-binder-rabbit-intro){:target="_blank"}

## 依存関係の追加
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-stream</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-stream-binder-rabbit</artifactId>
</dependency>
```

## Bindingの設定
```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
  cloud:
    stream:
      bindings:
        log-in-0:
          destination: log-exchange
          group: log-queue
        log-out-0:
          destination: log-exchange
          group: log-queue
```

## StreamListerを使用してProducerを実装
```java
@Component
public class SpringCloudStreamBinderRabbitIntroProducer {

    private final StreamBridge streamBridge;

    public SpringCloudStreamBinderRabbitIntroProducer(StreamBridge streamBridge) {
        this.streamBridge = streamBridge;
    }

    public void log(String message) {
        streamBridge.send("log-out-0", message);
    }
}
```

## Consumerを実装してBean定義
```java
@Component
public class SpringCloudStreamBinderRabbitIntroListener {

    @Bean
    public Consumer<String> log() {
        return m -> {
            // Let's make a great code!
        };
    }
}
```

## RabbitMQのセットアップ（オプション）
```shell
docker run --name rabbitmq -h my-rabbit -d -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:3-management
```
