# Log4j2 with Spring Boot
{:.no_toc}

* toc
{:toc}

## 依存関係
spring-boot-starter-loggingにLogbackが含まれているため取り除く。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

## 基本的なAPI
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

Logger log = LoggerFactory.getLogger(Programacho.class);
log.info("Hello Log4j2 with Spring Boot!");
```

## 基本的なlog4j2.xml（標準出力 & ファイル）
log4j2.xmlはsrc/main/resourceに配置することで自動的に読み込まれる。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT" follow="true">
            <PatternLayout pattern="%d [%thread] %-5level %logger{35} - %m%n"/>
        </Console>
        <File name="FILE" fileName="log/programacho.log">
            <PatternLayout pattern="%d [%thread] %-5level %logger{35} - %m%n"/>
        </File>
    </Appenders>

    <Loggers>
        <Root level="INFO">
            <AppenderRef ref="STDOUT"/>
            <AppenderRef ref="FILE"/>
        </Root>
    </Loggers>
</Configuration>
```

Rootタグのlevel要素をDEBUGにすると各種DEBUGログが出力される。

## 特定のクラス／パッケージのログレベルを変更する
loggerタグでFQCNを指定することで特定のクラスのログレベルを変更できる。

```xml
<Loggers>
    <Logger name="com.programacho.Programacho" level="DEBUG"/>
</Loggers>
```

FQCNではなくパッケージ名を指定することでパッケージ単位でログレベルを変更できる。

```xml
<Loggers>
    <Logger name="com.programacho" level="DEBUG"/>
</Loggers>
```

application.ymlでも同様に設定できる。Spring Bootではこちらが一般的。
```yml
logging:
  level:
    com.programacho.Programacho: DEBUG
```

## Springプロファイルでログ出力設定を切り替える
log4j2.xmlでプロファイルに応じて設定を追加する。

```xml
<Root level="INFO">
    <AppenderRef ref="STDOUT"/>

    <!-- prodプロファイルのみファイルにもログを出力する。 -->
    <SpringProfile name="prod">
        <AppenderRef ref="FILE"/>
    </SpringProfile>
</Root>
```

またはapplication.ymlでプロファイルに応じて設定ファイルを呼び分けることもできる。  
プロファイルでログ構造が大きく異なる場合はこちらを検討する。

```yml
spring.config.activate.on-profile: prod
logging:
  config: classpath:log4j2-prod.xml
```

## Springプロパティを取得する
```xml
<Properties>
  <Property name="appName">${spring:spring.application.name}</Property>
</Properties>

<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT" follow="true">
        <PatternLayout pattern="%d [${appName}] [%thread] %-5level %logger{35} - %m%n"/>
    </Console>
</Appenders>
```

```yml
spring:
  applicaiton:
    name: pmacho-app
```

```
2023-07-18 16:37:19,412 [pmacho-app] [main] INFO com.programacho.Programacho - programacho!
```
