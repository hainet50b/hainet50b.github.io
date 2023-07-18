# Logback with Spring Boot
{:.no_toc}

* toc
{:toc}

## 依存関係
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

spring-boot-starter-loggingはspring-boot-starterに含まれているため明示的に加える必要はない。

```shell
mvn dependency:tree

org.springframework.boot:spring-boot-starter:jar:3.1.1:compile
+- org.springframework.boot:spring-boot-starter-logging:jar:3.1.1:compile
|  +- ch.qos.logback:logback-classic:jar:1.4.8:compile
|  |  \- ch.qos.logback:logback-core:jar:1.4.8:compile
|  +- org.apache.logging.log4j:log4j-to-slf4j:jar:2.20.0:compile
|  |  \- org.apache.logging.log4j:log4j-api:jar:2.20.0:compile
|  \- org.slf4j:jul-to-slf4j:jar:2.0.7:compile
```

## 基本的なAPI
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

Logger log = LoggerFactory.getLogger(Programacho.class);
log.info("Hello Logback with Spring Boot!");
```

## 基本的なlogback.xml（標準出力 & ファイル）
logback.xmlはsrc/main/resourceに配置することで自動的に読み込まれる。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>
    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>./log/programacho.log</file>
        <encoder>
            <pattern>%d [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```

rootタグのlevel要素をDEBUGにすると各種DEBUGログが出力される。

## 特定のクラス／パッケージのログレベルを変更する
loggerタグでFQCNを指定することで特定のクラスのログレベルを変更できる。

```xml
<configuration>
    <logger name="com.programacho.Programacho" level="DEBUG" />
</configuration>
```

FQCNではなくパッケージ名を指定することでパッケージ単位でログレベルを変更できる。

```xml
<configuration>
    <logger name="com.programacho" level="DEBUG" />
</configuration>
```

application.ymlでも同様に設定できる。Spring Bootではこちらが一般的。
```yml
logging:
  level:
    com.programacho.Programacho: DEBUG
```

## Springプロファイルでログ出力設定を切り替える
logback.xmlでプロファイルに応じて設定を追加する。

```xml
<root level="INFO">
    <appender-ref ref="STDOUT"/>
</root>

<!-- prodプロファイルのみファイルにもログを出力する。 -->
<springProfile name="prod">
    <root level="INFO">
        <appender-ref ref="FILE"/>
    </root>
</springProfile>
```

またはapplication.ymlでプロファイルに応じて設定ファイルを呼び分けることもできる。  
プロファイルでログ構造が大きく異なる場合はこちらを検討する。

```yml
spring.config.activate.on-profile: prod
logging:
  config: classpath:logback-prod.xml
```

## Springプロパティを取得する
```xml
<springProperty
        name="appName"
        scope="context"
        source="spring.application.name"
        defaultValue="my-app"
/>

<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
        <pattern>%d [${appName}] [%thread] %-5level %logger{35} - %msg %n</pattern>
    </encoder>
</appender>
```

```yml
spring:
  applicaiton:
    name: pmacho-app
```

```
2023-07-16 17:14:26,270 [pmacho-app] [main] INFO c.p.Programacho - programacho!

# spring.application.nameが設定されていないとデフォルト値が採用される。
2023-07-16 17:17:45,513 [my-app] [main] INFO c.p.Programacho - programacho! 
```
