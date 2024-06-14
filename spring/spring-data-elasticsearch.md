# Spring Data Elasticsearch
{:.no_toc}

* toc
{:toc}

## 課題メモ
- Elasticsearch APIは非同期で動作するため、更新後に検索する上手な方法を探りたい。
- 例外ハンドリングの基本的な指針を設計したい。
- タイムアウトの検証をしたい。

## 依存関係の追加
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

## Elasticsearchクライアントの設定
公式ドキュメント：[Elasticsearch Clients :: Spring Data Elasticsearch](https://docs.spring.io/spring-data/elasticsearch/reference/elasticsearch/clients.html){:target="_blank"}

`ElasticsearchConfiguration` を拡張したクラスで `clientConfiguration` メソッドをオーバーライドする。

```java
@Configuration
public class ElasticsearchClientConfig extends ElasticsearchConfiguration {

    @Override
    public ClientConfiguration clientConfiguration() {
        return ClientConfiguration.builder()
                .connectedTo("localhost:9200")
                .build();
    }
}
```

ビルダーはHTTPS通信やBasic認証にも対応している。

```java
@Configuration
public class ElasticsearchClientConfig extends ElasticsearchConfiguration {

    @Override
    public ClientConfiguration clientConfiguration() {
        return ClientConfiguration.builder()
                .connectedTo("localhost:9200")
                .usingSsl()
                .withBasicAuth("elastic", "changeme")
                .build();
    }
}
```

開発環境で証明書検証を無効化する場合は `SSLContect` をロードして対応する。

```java
@Configuration
public class ElasticsearchClientConfig extends ElasticsearchConfiguration {

    @Override
    public ClientConfiguration clientConfiguration() {
        try {
            X509TrustManager[] trustManagers = {
                    new X509TrustManager() {
                        @Override
                        public void checkClientTrusted(X509Certificate[] chain, String authType) {
                        }

                        @Override
                        public void checkServerTrusted(X509Certificate[] chain, String authType) {
                        }

                        @Override
                        public X509Certificate[] getAcceptedIssuers() {
                            return new X509Certificate[0];
                        }
                    }
            };

            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, trustManagers, null);

            return ClientConfiguration.builder()
                    .connectedTo("localhost:9200")
                    .usingSsl()
                    .withBasicAuth("elastic", "changeme")
                    .withClientConfigurer(ElasticsearchClients.ElasticsearchHttpClientConfigurationCallback.from(b ->
                            b.setSSLContext(sslContext)
                    ))
                    .build();
        } catch (NoSuchAlgorithmException | KeyManagementException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## Elasticsearchオブジェクトマッピングの作成
公式ドキュメント：[Elasticsearch Object Mapping :: Spring Data Elasticsearch](https://docs.spring.io/spring-data/elasticsearch/reference/elasticsearch/object-mapping.html){:target="_blank"}

操作対象インデックスをSpELで動的に指定できる。  
`@PersistenceCreator` が付与されたコンストラクタはオブジェクト生成時に優先的に使用される。

インデックスを横断して検索するために、エンティティでインデックスパターンを定義しておく。

```java
@Document(indexName = "users-#{T(java.time.LocalDate).now().format(T(java.time.format.DateTimeFormatter).ofPattern(\"yyyy.MM.dd\"))}")
public class User implements Queryable {

    public static String getIndexPattern() {
        return "users-*";
    }

    @Id
    private String elasticsearchId;

    @Field(name = "id", type = FieldType.Integer)
    private Integer id;

    @Field(type = FieldType.Keyword)
    private String name;

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    @PersistenceCreator
    public User(String elasticsearchId, Integer id, String name) {
        this.elasticsearchId = elasticsearchId;
        this.id = id;
        this.name = name;
    }

    public String getElasticsearchId() {
        return elasticsearchId;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }
}

public interface Queryable {

    static String getIndexPattern() {
        throw new UnsupportedOperationException("Give an index pattern.");
    };
}
```

## Elasticsearchクライアントのログ出力
- Spring Data Elasticsearch公式ドキュメント：[Elasticsearch Clients :: Spring Data Elasticsearch](https://docs.spring.io/spring-data/elasticsearch/reference/elasticsearch/clients.html#elasticsearch.clients.logging){:target="_blank"}
- Elasticsearch Java API Client公式ドキュメント：[Logging \|Elasticsearch Java API Client \| Elastic](https://www.elastic.co/guide/en/elasticsearch/client/java-api-client/current/java-rest-low-usage-logging.html#_restclient_debug_logs){:target="_blank"}

リクエストラインとステータスラインだけを可視化する場合

```yml
logging:
  level:
    tracer: trace
```

```
2024-06-14T13:30:55.017+09:00 DEBUG 4348 --- [spring-data-elasticsearch-demo] [           main] org.elasticsearch.client.RestClient      : request [PUT https://localhost:9200/users-2024.06.14/_doc/elasticsearch-id?refresh=false] returned [HTTP/1.1 201 Created]
```

リクエストボディとレスポンスボディも可視化する場合

```yml
logging:
  level:
    org.elasticsearch.client.RestClient: debug
```

```
2024-06-14T13:34:48.908+09:00 TRACE 1888 --- [spring-data-elasticsearch-demo] [           main] tracer                                   : curl -iX PUT 'https://localhost:9200/users-2024.06.14/_doc/elasticsearch-id?refresh=false' -d '{"_class":"com.programacho.springdataelasticsearchdemo.User","elasticsearchId":"elasticsearch-id","id":1,"name":"hainet50b"}'
# HTTP/1.1 201 Created
# Location: /users-2024.06.14/_doc/elasticsearch-id
# X-elastic-product: Elasticsearch
# content-type: application/vnd.elasticsearch+json;compatible-with=8
# content-length: 164
#
# {"_index":"users-2024.06.14","_id":"elasticsearch-id","_version":1,"result":"created","_shards":{"total":2,"successful":1,"failed":0},"_seq_no":0,"_primary_term":1}
```

## ElasticsearchOperationsの使用
公式ドキュメント：[Elasticsearch Operations :: Spring Data Elasticsearch](https://docs.spring.io/spring-data/elasticsearch/reference/elasticsearch/template.html){:target="_blank"}

`ElasticsearchOperations` を注入して使用する。

```java
@Service
public class UserService {

    private final ElasticsearchOperations elasticsearchOperations;
    
    public UserService(ElasticsearchOperations elasticsearchOperations) {
        this.elasticsearchOperations = elasticsearchOperations;
    }

    public void save(User user) {
        elasticsearchOperations.save(user);
    }
}
```

### search(query, clazz, index)（全件検索）
`IndexCoordinates` を含むメソッドを使用してインデックスを横断して検索する。

```java
SearchHits<User> hits = elasticsearchOperations.search(
        new CriteriaQuery(new Criteria()),
        User.class,
        IndexCoordinates.of(User.getIndexPattern())
);

hits.forEach(h -> {
    System.out.println(h.getId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(h.getContent().getId()); // 1, ...
    System.out.println(h.getContent().getName()); // hainet50b, ...
});
```

### get(id, clazz, index)（Elasticsearch IDによる単数検索）
```java
User user = elasticsearchOperations.get(
        "elasticsearch-id",
        User.class,
        IndexCoordinates.of("users-2024.10.24")
);

System.out.println(user.getId()); // 1
System.out.println(user.getName()); // hainet50b
```

### search(query, clazz, index)（任意の項目による複数検索）
```java
Criteria criteria = new Criteria("id").is(1);
CriteriaQuery query = new CriteriaQuery(criteria);
SearchHits<User> hits = elasticsearchOperations.search(
        query, 
        User.class,
        IndexCoordinates.of(User.getIndexPattern())
);

hits.forEach(h -> {
    System.out.println(h.getId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(h.getContent().getId()); // 1, ...
    System.out.println(h.getContent().getName()); // hainet50b, ...
});
```

### save(entity)（Elasticsearch IDを指定せず単数挿入）
```java
User user = elasticsearchOperations.save(
        new User(1, "hainet50b")
);

System.out.println(user.getElasticsearchId()); // IiROB5ABAHrfptHDxmVC
System.out.println(user.getId()); // 1
System.out.println(user.getName()); // hainet50b
```

### save(entities...)（Elasticsearch IDを指定せず複数挿入）
```java
Iterable<User> users = elasticsearchOperations.save(
        new User(1, "hainet50b"),
        new User(2, "programacho.com")
);

users.forEach(u -> {
    System.out.println(u.getElasticsearchId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(u.getId()); // 1, 2
    System.out.println(u.getName()); // hainet50b, programacho.com
});
```

### save(entity)（Elasticsearch IDを指定して単数挿入・更新）
```java
User user = elasticsearchOperations.save(
        new User("elasticsearch-id", 1, "hainet50b")
);

System.out.println(user.getElasticsearchId()); // elasticsearch-id
System.out.println(user.getId()); // 1
System.out.println(user.getName()); // hainet50b
```

### save(entities...)（Elasticsearch IDを指定して複数挿入・更新）
```java
Iterable<User> users = elasticsearchOperations.save(
        new User("elasticsearch-id1", 1, "hainet50b"),
        new User("elasticsearch-id2", 2, "programacho.com")
);

users.forEach(u -> {
    System.out.println(u.getElasticsearchId()); // elasticsearch-id1, elasticsearch-id2
    System.out.println(u.getId()); // 1, 2
    System.out.println(u.getName()); // hainet50b, programacho.com
});
```

### update(entity, index)（Elasticsearch IDを指定して単数更新）
```java
User user = elasticsearchOperations.get(
        "elasticsearch-id",
        User.class,
        IndexCoordinates.of("users-2024.10.24")
);
user.setId(2);
user.setName(UUID.randomUUID().toString());

UpdateResponse response = elasticsearchOperations.update(
        user,
        IndexCoordinates.of("users-2024.10.24")
);

System.out.println(response.getResult()); // UPDATED or NOOP
```

### delete(entity, index)（Elasticsearch IDを指定して単数削除）
```java
User user = elasticsearchOperations.get(
        "elasticsearch-id",
        User.class,
        IndexCoordinates.of("users-2024.10.24")
);

String elasticsearchId = elasticsearchOperations.delete(
        user,
        IndexCoordinates.of("users-2024.10.24")
);

System.out.println(elasticsearchId); // elasticsearch-id
```

### delete(query, clazz, index)（任意の項目による複数削除）
```java
Criteria criteria = new Criteria("id").is(4);
CriteriaQuery query = new CriteriaQuery(criteria);
ByQueryResponse response = elasticsearchOperations.delete(
        DeleteQuery.builder(query).build(),
        User.class,
        IndexCoordinates.of(User.getIndexPattern())
);

System.out.println(response.getDeleted()); // => 0
```

## 代表的な例外
Spring Data例外の階層構造：[DAO Support :: Spring Framework](https://docs.spring.io/spring-framework/reference/data-access/dao.html#dao-exceptions){:target="_blank"}

| 事象 | 例外 |
| :--- | :--- |
| 対象のインデックスが存在しない | org.springframework.data.elasticsearch.NoSuchIndexException |
| Elasticsearchと通信できない | org.springframework.dao.DataAccessResourceFailureException |
| Elasticsearchとの通信がタイムアウトした | TODO |

### 対象のインデックスが存在しない
サンプルコード

```java
elasticsearchOperations.get(
        "elasticsearch-id",
        User.class,
        IndexCoordinates.of("users-9999.12.31")
);
```

スタックトレース

```
org.springframework.data.elasticsearch.NoSuchIndexException: Index users-9999.12.31 not found.
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchExceptionTranslator.translateExceptionIfPossible(ElasticsearchExceptionTranslator.java:91) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchExceptionTranslator.translateException(ElasticsearchExceptionTranslator.java:65) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.execute(ElasticsearchTemplate.java:685) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.get(ElasticsearchTemplate.java:140) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at com.programacho.springdataelasticsearchdemo.SpringDataElasticsearchDemoApplication.lambda$run$0(SpringDataElasticsearchDemoApplication.java:20) ~[classes/:na]
	at org.springframework.boot.SpringApplication.lambda$callRunner$5(SpringApplication.java:790) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.util.function.ThrowingConsumer$1.acceptWithException(ThrowingConsumer.java:83) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.util.function.ThrowingConsumer.accept(ThrowingConsumer.java:60) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.util.function.ThrowingConsumer$1.accept(ThrowingConsumer.java:88) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:798) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:789) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.lambda$callRunners$3(SpringApplication.java:774) ~[spring-boot-3.3.0.jar:3.3.0]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:184) ~[na:na]
	at java.base/java.util.stream.SortedOps$SizedRefSortingSink.end(SortedOps.java:357) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:510) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:499) ~[na:na]
	at java.base/java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:151) ~[na:na]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:174) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[na:na]
	at java.base/java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:596) ~[na:na]
	at org.springframework.boot.SpringApplication.callRunners(SpringApplication.java:774) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:342) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1363) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1352) ~[spring-boot-3.3.0.jar:3.3.0]
	at com.programacho.springdataelasticsearchdemo.SpringDataElasticsearchDemoApplication.main(SpringDataElasticsearchDemoApplication.java:14) ~[classes/:na]
```

### Elasticsearchと通信できない
スタックトレース

```
org.springframework.dao.DataAccessResourceFailureException: Connection refused: getsockopt
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchExceptionTranslator.translateExceptionIfPossible(ElasticsearchExceptionTranslator.java:111) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchExceptionTranslator.translateException(ElasticsearchExceptionTranslator.java:65) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.execute(ElasticsearchTemplate.java:685) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.get(ElasticsearchTemplate.java:140) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.core.AbstractElasticsearchTemplate.get(AbstractElasticsearchTemplate.java:272) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at com.programacho.springdataelasticsearchdemo.SpringDataElasticsearchDemoApplication.lambda$run$0(SpringDataElasticsearchDemoApplication.java:20) ~[classes/:na]
	at org.springframework.boot.SpringApplication.lambda$callRunner$5(SpringApplication.java:790) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.util.function.ThrowingConsumer$1.acceptWithException(ThrowingConsumer.java:83) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.util.function.ThrowingConsumer.accept(ThrowingConsumer.java:60) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.util.function.ThrowingConsumer$1.accept(ThrowingConsumer.java:88) ~[spring-core-6.1.8.jar:6.1.8]
	at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:798) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.callRunner(SpringApplication.java:789) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.lambda$callRunners$3(SpringApplication.java:774) ~[spring-boot-3.3.0.jar:3.3.0]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:184) ~[na:na]
	at java.base/java.util.stream.SortedOps$SizedRefSortingSink.end(SortedOps.java:357) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:510) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:499) ~[na:na]
	at java.base/java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:151) ~[na:na]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:174) ~[na:na]
	at java.base/java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[na:na]
	at java.base/java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:596) ~[na:na]
	at org.springframework.boot.SpringApplication.callRunners(SpringApplication.java:774) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:342) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1363) ~[spring-boot-3.3.0.jar:3.3.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1352) ~[spring-boot-3.3.0.jar:3.3.0]
	at com.programacho.springdataelasticsearchdemo.SpringDataElasticsearchDemoApplication.main(SpringDataElasticsearchDemoApplication.java:14) ~[classes/:na]
Caused by: java.lang.RuntimeException: Connection refused: getsockopt
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchExceptionTranslator.translateException(ElasticsearchExceptionTranslator.java:64) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	... 24 common frames omitted
Caused by: java.net.ConnectException: Connection refused: getsockopt
	at org.elasticsearch.client.RestClient.extractAndWrapCause(RestClient.java:934) ~[elasticsearch-rest-client-8.13.4.jar:8.13.4]
	at org.elasticsearch.client.RestClient.performRequest(RestClient.java:304) ~[elasticsearch-rest-client-8.13.4.jar:8.13.4]
	at org.elasticsearch.client.RestClient.performRequest(RestClient.java:292) ~[elasticsearch-rest-client-8.13.4.jar:8.13.4]
	at co.elastic.clients.transport.rest_client.RestClientHttpClient.performRequest(RestClientHttpClient.java:91) ~[elasticsearch-java-8.13.4.jar:na]
	at co.elastic.clients.transport.ElasticsearchTransportBase.performRequest(ElasticsearchTransportBase.java:144) ~[elasticsearch-java-8.13.4.jar:na]
	at co.elastic.clients.elasticsearch.ElasticsearchClient.get(ElasticsearchClient.java:910) ~[elasticsearch-java-8.13.4.jar:na]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.lambda$get$0(ElasticsearchTemplate.java:140) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	at org.springframework.data.elasticsearch.client.elc.ElasticsearchTemplate.execute(ElasticsearchTemplate.java:683) ~[spring-data-elasticsearch-5.3.0.jar:5.3.0]
	... 23 common frames omitted
Caused by: java.net.ConnectException: Connection refused: getsockopt
	at java.base/sun.nio.ch.Net.pollConnect(Native Method) ~[na:na]
	at java.base/sun.nio.ch.Net.pollConnectNow(Net.java:682) ~[na:na]
	at java.base/sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:973) ~[na:na]
	at org.apache.http.impl.nio.reactor.DefaultConnectingIOReactor.processEvent(DefaultConnectingIOReactor.java:174) ~[httpcore-nio-4.4.16.jar:4.4.16]
	at org.apache.http.impl.nio.reactor.DefaultConnectingIOReactor.processEvents(DefaultConnectingIOReactor.java:148) ~[httpcore-nio-4.4.16.jar:4.4.16]
	at org.apache.http.impl.nio.reactor.AbstractMultiworkerIOReactor.execute(AbstractMultiworkerIOReactor.java:351) ~[httpcore-nio-4.4.16.jar:4.4.16]
	at org.apache.http.impl.nio.conn.PoolingNHttpClientConnectionManager.execute(PoolingNHttpClientConnectionManager.java:221) ~[httpasyncclient-4.1.5.jar:4.1.5]
	at org.apache.http.impl.nio.client.CloseableHttpAsyncClientBase$1.run(CloseableHttpAsyncClientBase.java:64) ~[httpasyncclient-4.1.5.jar:4.1.5]
	at java.base/java.lang.Thread.run(Thread.java:1583) ~[na:na]
```

### Elasticsearchとの通信がタイムアウトした
スタックトレース

```
TODO
```

## Queryの種類

### CriteriaQuery

### StringQuery

### NativeQuery
