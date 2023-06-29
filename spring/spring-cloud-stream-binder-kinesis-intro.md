# Spring Cloud Stream Binder Kinesis入門
{:.no_toc}

* toc
{:toc}

公式サンプルリポジトリ：[kinesis-produce-comsume \| GitHub](https://github.com/spring-cloud/spring-cloud-stream-samples/tree/main/kinesis-samples/kinesis-produce-consume){:target="_blank"}

## 依存関係
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
    <artifactId>spring-cloud-stream-binder-kinesis</artifactId>
    <version>4.0.0</version>
</dependency>
```

## Amazon Kinesis Data Streamの指定
ローカル環境にAWS CLIで設定を行っていると自動で設定される。  
TODO: 仕組みが分かっていないので調査する。

## Bindingの設定
```yaml
spring:
  cloud:
    function:
      definition: log
    stream:
      bindings:
        # ProducerのBean名を指定
        log-out-0:
          # Amazon Kinesis Data Stream名を指定
          destination: pmacho-stream
          content-type: application/json
          producer:
            header-mode: none
```

## Producer実装
```java
@Component
public class LogSource {

    private final BlockingQueue<Map<String, String>> logEvent = new LinkedBlockingQueue<>();

    @Bean
    public Supplier<Map<String, String>> log() {
        return logEvent::poll;
    }

    public void sendLog(Map<String, String> log) {
        logEvent.add(log);
    }
}
```

## アプリケーション起動
アプリケーションを起動するとデータストリームが自動で生成される。

```shell
aws kinesis list-streams \
  --query "StreamSummaries[].[StreamName,StreamStatus,StreamCreationTimestamp]" \
  --output text

# destination名がデータストリーム名として採用される。
pmacho-stream   ACTIVE  2023-06-28T14:38:52+09:00
```
