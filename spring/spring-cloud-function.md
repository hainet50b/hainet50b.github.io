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
公式ドキュメント：[Introduction](https://docs.spring.io/spring-cloud-function/docs/current/reference/html/aws.html){:target="_blank"} ([日本語版](https://spring.pleiades.io/spring-cloud-function/docs/current/reference/html/aws.html){:target="_blank"})
公式サンプル：[function-sample-aws \| GitHub](https://github.com/spring-cloud/spring-cloud-function/tree/main/spring-cloud-function-samples/function-sample-aws){:target="_blank"}
サンプルリポジトリ：[spring-cloud-function-aws \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-cloud-gym/spring-cloud-function-gym/spring-cloud-function-aws){:target="_blank"}

### 依存関係
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-function-adapter-aws</artifactId>
</dependency>
<dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-lambda-java-events</artifactId>
    <version>3.11.2</version>
</dependency>
<dependency>
    <groupId>com.amazonaws</groupId>
    <artifactId>aws-lambda-java-core</artifactId>
    <version>1.2.2</version>
</dependency>
```

AWS向けのJarファイルをビルドするプラグインを追加する。
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-deploy-plugin</artifactId>
    <configuration>
        <skip>true</skip>
    </configuration>
</plugin>
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot.experimental</groupId>
            <artifactId>spring-boot-thin-layout</artifactId>
            <version>1.0.30.RELEASE</version>
        </dependency>
    </dependencies>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <configuration>
        <createDependencyReducedPom>false</createDependencyReducedPom>
        <shadedArtifactAttached>true</shadedArtifactAttached>
        <shadedClassifierName>aws</shadedClassifierName>
    </configuration>
</plugin>
```

### ビルド
```shell
./mvnw clean package

ls -la target 
-rw-r--r--@ 1 hainet50b staff 18270083 Jun 22 21:36 spring-cloud-function-aws-0.0.1-SNAPSHOT-aws.jar
-rw-r--r--@ 1 hainet50b staff    11481 Jun 22 21:36 spring-cloud-function-aws-0.0.1-SNAPSHOT.jar
```

### デプロイ
AWS lambda関数を作成する。

![spring-cloud-function-a9c7cac9f568.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-a9c7cac9f568.png)

`spring-cloud-function-aws-0.0.1-SNAPSHOT-aws.jar`をアップロードする。

![spring-cloud-function-b67637012f1b.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-b67637012f1b.png)

ハンドラを`org.springframework.cloud.function.adapter.aws.FunctionInvoker::handleRequest`に設定する。

![spring-cloud-function-a4894810192f.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-a4894810192f.png)

イベントJSONに`{"name": "hainet50b"}`を設定してテストを実行する。

![spring-cloud-function-e2532b7dce3f.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-e2532b7dce3f.png)

およそ3000msで完了した。

![spring-cloud-function-7c18d0b91675.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-7c18d0b91675.png)

## AWS Lambdaへのデプロイ（Native Image）
サンプルリポジトリ：[spring-cloud-function-aws-native-image \| GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-cloud-gym/spring-cloud-function-gym/spring-cloud-function-aws-native-image){:target="_blank"}

### 依存関係
Native Imageをビルドするプラグインを追加する。
```xml
<plugin>
    <groupId>org.graalvm.buildtools</groupId>
    <artifactId>native-maven-plugin</artifactId>
</plugin>
```

### イメージの作成
イメージの作成には筆者のM2 Mac環境で17分44秒かかった。
```shell
./mvnw -Pnative spring-boot:build-image

docker images
REPOSITORY                             TAG            IMAGE ID     CREATED      SIZE
spring-cloud-function-aws-native-image 0.0.1-SNAPSHOT 3efae5c2127a 43 years ago 80.7MB
```

Macの場合、`/var/run/docker.sock`がないためシンボリックリンクを作成してからビルドする。
```shell
sudo ln -s $HOME/.docker/run/docker.sock /var/run/docker.sock
```

### ECR(Elastic Container Registry)へプッシュ
ECRの管理画面からリポジトリを作成する。
![spring-cloud-function-8e98aae07a56.png](https://programacho.blob.core.windows.net/images/spring-cloud-function-8e98aae07a56.png)

ECRに作成したイメージをプッシュする。
```shell
# ログイン
aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin ************.dkr.ecr.ap-northeast-1.amazonaws.com

# タグ付与
docker tag spring-cloud-function-aws-native-image:0.0.1-SNAPSHOT ************.dkr.ecr.ap-northeast-1.amazonaws.com/spring-cloud-function-aws-native-image:0.0.1-SNAPSHOT

# プッシュ
docker push ************.dkr.ecr.ap-northeast-1.amazonaws.com/spring-cloud-function-aws-native-image:0.0.1-SNAPSHOT
```

### AWS Lambda関数を作成する
TODO