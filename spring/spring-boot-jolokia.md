# Spring Boot Jolokia
{:.no_toc}

* toc
{:toc}

## 依存関係
```xml
<!-- Spring Boot 3.x系 -->
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-support-spring</artifactId>
    <version>2.0.2</version>
</dependency>

<!-- Spring Boot 2.x系 -->
<dependency>
    <groupId>org.jolokia</groupId>
    <artifactId>jolokia-core</artifactId>
</dependency>
```

## 設定ファイル
```yml
management:
  endpoints:
    web:
      exposure:
        include: health, jolokia
```

## 参考curlコマンド
```sh
curl localhost:8080/actuator/jolokia/read/java.lang:type=Threading/ThreadCount

{
  "request": {
    "mbean": "java.lang:type=Threading",
    "attribute": "ThreadCount",
    "type": "read"
  },
  "value": 29,
  "status": 200,
  "timestamp": 1711441019
}
```