# Spring AMQP入門
{:.no_toc}

* toc
{:toc}

サンプルリポジトリ：[spring-amqp-intro \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-amqp-gym/spring-amqp-intro){:target="_blank"}

## 依存関係の追加
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

## RabbitMQの接続情報を定義
```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
```

## 対象となるQueueをBean定義
```java
@Configuration
public class QueueConfig {

    // ProducerとConsumerで同様のキュー名を参照できるようにする。
    public static final String SPRING_AMQP_INTRO = "spring-amqp-intro";

    @Bean
    public Queue springAmqpIntro() {
        return new Queue(SPRING_AMQP_INTRO);
    }
}
```

## Producerを実装
```java
private final RabbitTemplate rabbitTemplate;

private final Queue queue;

public SpringAmqpIntroProducer(
        RabbitTemplate rabbitTemplate,
        Queue queue) {
    this.rabbitTemplate = rabbitTemplate;
    this.queue = queue;
}

public void produce(@RequestBody String message) {
    rabbitTemplate.convertAndSend(queue.getName(), message);
}
```

## Consumerを実装してBean定義
Consumerを実装する。

```java
@RabbitListener(queues = QueueConfig.SPRING_AMQP_INTRO)
public class SpringAmqpIntroConsumer {

    @RabbitHandler
    public void consume(String message) {
        // Let's do something!
    }
}
```

実装したConsumerをBean定義する。  
割り切ってJava ConfigクラスをListenerと名付けてControllerと同様に取り扱っても良い。

```java
@Configuration
public class SpringAmqpIntroListener {

    @Bean
    public SpringAmqpIntroConsumer consumer() {
        return new SpringAmqpIntroConsumer();
    }
}
```

## RabbitMQのセットアップ（オプション）
```shell
docker run --name rabbitmq -h my-rabbit -d -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:3-management
```
