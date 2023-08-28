# Java スレッドダンプ解析

## スレッドダンプの取得

### jstackコマンド
```shell
jps
54138 PmachoApplication

jstack 54138
```

### Spring Boot Actuator
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```yml
management:
  endpoints:
    web:
      exposure:
        include: "threaddump"
```

Spring Boot 2.2からAcceptヘッダを付与することでテキスト形式で取得できるようなり実用的となった。
- [Plain text support for Thread dump endpoint](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.2-Release-Notes#plain-text-support-for-thread-dump-endpoint){:target="_blank"}
- [Retrieving the Thread Dump as Text](https://docs.spring.io/spring-boot/docs/current/actuator-api/htmlsingle/#threaddump.retrieving-text){:target="_blank"}

```shell
curl -H "Accept: text/plain" localhost:8080/actuator/threaddump > threaddump-$(date +%s)

ls -la
-rw-r--r--@  1 hainet50b  staff  31500 Aug 28 10:33 threaddump-1693186412
```

## スレッドダンプの解析
グローバルスタンダードではないものの、Samuraiが使いやすい。

[samurai \| GitHub](https://github.com/yusuke/samurai){:target="_blank"}

```shell
java -jar samurai-swing-2021.9.jar 
```
