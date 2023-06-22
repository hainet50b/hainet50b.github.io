# Spring Cloud Function
{:.no_toc}

* toc
{:toc}

公式ドキュメント：[Spring Cloud Function](https://docs.spring.io/spring-cloud-function/docs/current/reference/html/spring-cloud-function.html){:target="_blank"} ([日本語版](https://spring.pleiades.io/spring-cloud-function/docs/current/reference/html/spring-cloud-function.html){:target="_blank"})

## ローカル環境での起動
サンプルリポジトリ：[spring-cloud-function-intro \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-cloud-gym/spring-cloud-function-gym/spring-cloud-function-intro){:target="_blank"}

### 依存関係
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>2022.0.3</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-function-web</artifactId>
</dependency>
```

### 基本的な実装
```java
@SpringBootApplication
public class SpringCloudFunctionBasicApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringCloudFunctionGymApplication.class, args);
    }

    @Bean
    public Function<String, String> hello() {
        return v -> String.format("Hello %s. Welcome to Spring Cloud Function!", v);
    }
}
```

### 起動方法
```shell
./mvnw spring-boot:run

curl localhost:8080/hello -H "Content-Type: text/plain" -d hainet50b
Hello hainet50b. Welcome to Spring Cloud Function!
```

## AWS Lambdaへのデプロイ（Jar）
サンプルリポジトリ：[spring-cloud-function-aws \| GitHub](TODO){:target="_blank"}

## AWS Lambdaへのデプロイ（Native Image）
サンプルリポジトリ：[spring-cloud-function-aws-native-image \| GitHub](){:target="_blank"}
