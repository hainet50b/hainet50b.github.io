# Spring Data Elasticsearch
{:.no_toc}

* toc
{:toc}

## 課題メモ
- Elasticsearch APIは非同期で動作するため、更新後に検索する上手な方法を探りたい。
- インデックスを横断して検索する方法を探りたい。

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

```java
@Document(indexName = "users-#{T(java.time.LocalDate).now().format(T(java.time.format.DateTimeFormatter).ofPattern(\"yyyy.MM.dd\"))}")
public class User {

    @Id
    private String elasticsearchId;

    @Field(name = "id", type = FieldType.Integer)
    private final Integer id;

    @Field(type = FieldType.Keyword)
    private final String name;

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
}
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

### search(query, clazz)（全件検索）
```java
SearchHits<User> hits = elasticsearchOperations.search(new CriteriaQuery(new Criteria()), User.class);

hits.forEach(h -> {
    System.out.println(h.getId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(h.getContent().getId()); // 1, ...
    System.out.println(h.getContent().getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227, ...
});
```

### get(id, clazz)（Elasticsearch IDによる単数検索）
```java
User user = elasticsearchOperations.get("IiROB5ABAHrfptHDxmVC", User.class);

System.out.println(user.getId()); // 1
System.out.println(user.getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227
```

### search(query, clazz)（任意の項目による複数検索）
```java
Criteria criteria = new Criteria("id").is(1);
CriteriaQuery query = new CriteriaQuery(criteria);
SearchHits<User> hits = elasticsearchOperations.search(query, User.class);

hits.forEach(h -> {
    System.out.println(h.getId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(h.getContent().getId()); // 1, ...
    System.out.println(h.getContent().getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227, ...
});
```

### save(entity)（Elasticsearch IDを指定せず単数挿入）
```java
User user = elasticsearchOperations.save(
        new User(1, UUID.randomUUID().toString())
);

System.out.println(user.getElasticsearchId()); // IiROB5ABAHrfptHDxmVC
System.out.println(user.getId()); // 1
System.out.println(user.getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227
```

### save(entities...)（Elasticsearch IDを指定せず複数挿入）
```java
Iterable<User> users = elasticsearchOperations.save(
        new User(1, UUID.randomUUID().toString()),
        new User(2, UUID.randomUUID().toString())
);

users.forEach(u -> {
    System.out.println(u.getElasticsearchId()); // IiROB5ABAHrfptHDxmVC, ...
    System.out.println(u.getId()); // 1, 2
    System.out.println(u.getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227, ...
});
```

### save(entity)（Elasticsearch IDを指定して単数挿入・更新）
```java
User user = elasticsearchOperations.save(
        new User("elasticsearch-id", 1, UUID.randomUUID().toString())
);

System.out.println(user.getElasticsearchId()); // elasticsearch-id
System.out.println(user.getId()); // 1
System.out.println(user.getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227
```

### save(entities...)（Elasticsearch IDを指定して複数挿入・更新）
```java
elasticsearchOperations.save(
        new User("elasticsearch-id1", 1, UUID.randomUUID().toString()),
        new User("elasticsearch-id2", 2, UUID.randomUUID().toString())
);

users.forEach(u -> {
    System.out.println(u.getElasticsearchId()); // elasticsearch-id1, elasticsearch-id2
    System.out.println(u.getId()); // 1, 2
    System.out.println(u.getName()); // afc45df7-09cb-4ba2-bc7a-dc7b561ae227, ...
});
```

### update(entity)（Elasticsearch IDを指定して単数更新）
```java
UpdateResponse response = elasticsearchOperations.update(
        new User("elasticsearch-id", 10, "hainet50b")
);

System.out.println(response.getResult()); // UPDATED or NOOP
```

### delete(id, clazz)（Elasticsearch IDを指定して単数削除）
```java
String elasticsearchId = elasticsearchOperations.delete("elasticsearch-id", User.class);

System.out.println(elasticsearchId); // elasticsearch-id
```

### delete(query, clazz)（任意の項目による複数削除）
```java
Criteria criteria = new Criteria("id").is(4);
CriteriaQuery query = new CriteriaQuery(criteria);
ByQueryResponse response = elasticsearchOperations.delete(DeleteQuery.builder(query).build(), User.class);

System.out.println(response.getDeleted()); // => 0
```

## Queryの種類

### CriteriaQuery

### StringQuery

### NativeQuery