# Spring AMQP入門
{:.no_toc}

* toc
{:toc}

サンプルリポジトリ：[spring-amqp-intro | GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-amqp-gym/spring-amqp-intro){:target="_blank"}

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
    public static final String SPRING_AMQP_BASIC = "spring-amqp-basic";

    @Bean
    public Queue springAmqpBasic() {
        return new Queue(SPRING_AMQP_BASIC);
    }
}
```

## Producerを実装
```java
private final RabbitTemplate rabbitTemplate;

private final Queue queue;

public SpringAmqpBasicProducer(
        RabbitTemplate rabbitTemplate,
        Queue queue) {
    this.rabbitTemplate = rabbitTemplate;
    this.queue = queue;
}

public void produce(@RequestBody String message) {
    rabbitTemplate.convertAndSend(queue.getName(), message);
}
```

## Consumerを実装
```java
@Component
@RabbitListener(queues = QueueConfig.SPRING_AMQP_BASIC)
public class SpringAmqpBasicConsumer {

    @RabbitHandler
    public void consume(String message) {
        // Let's do something!
    }
}
```

## RabbitMQのセットアップ（オプション）
```shell
docker run --name rabbitmq -h my-rabbit -d -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:3-management
```
