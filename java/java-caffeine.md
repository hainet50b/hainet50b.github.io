# Caffeine（キャッシュライブラリ）
{:.no_toc}

* toc
{:toc}

公式ドキュメント：[caffeine \| GitHub](https://github.com/ben-manes/caffeine){:target="_blank"}

## 依存関係
[Caffeine Cache \| Maven Repository](https://mvnrepository.com/artifact/com.github.ben-manes.caffeine/caffeine){:target="_blank"}

```xml
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
    <version>3.1.6</version>
</dependency>
```

## 基本的なAPI
エントリーの設定と取得

```java
Cache<String, String> cache = Caffeine.newBuilder().build();
cache.put("key", "value");
cache.getIfPresent("key"); // value
cache.getIfPresent("foo"); // null
```

エントリーの削除
```java
Cache<Object, Object> cache = Caffeine.newBuilder().build();

cache.put("foo", "value");
cache.put("bar", "value");
cache.put("baz", "value");

cache.invalidate("foo"); // {bar=value, baz=value}
cache.invalidateAll(List.of("foo", "bar")); // {baz=value}
cache.invalidateAll(); // []
```

getメソッドでヒットしない場合のキャッシュローダーを指定できる。

```java
Cache<String, String> cache = Caffeine.newBuilder().build();
cache.get("key", k -> "value"); // value (from Function)
cache.get("key", k -> "value"); // value (from Cache)
```

buildメソッドでキャッシュローダーを指定できる。

```java
LoadingCache<String, String> cache = Caffeine.newBuilder().build(k -> "value");
```

```java
public static void main(String[] args) {
    LoadingCache<String, String> cache = Caffeine.newBuilder().build(k -> generateUuid());

    measureTime(() -> cache.get("foo")); // 3000ms
    measureTime(() -> cache.get("foo")); // 0ms
    measureTime(() -> cache.get("foo")); // 0ms

    System.out.println(cache.asMap()); // {foo=00b49709-b132-4d6b-a661-f3f2f73eaac7}
}

private static String generateUuid() {
    try {
        TimeUnit.SECONDS.sleep(3);

        return UUID.randomUUID().toString();
    } catch (InterruptedException e) {
        throw new RuntimeException(e);
    }
}

private static void measureTime(Runnable runnable) {
    long start = System.currentTimeMillis();
    runnable.run();
    long end = System.currentTimeMillis();

    System.out.println(end - start + " ms");
}
```

## キャッシュ設定
[Eviction \| GitHub](https://github.com/ben-manes/caffeine/wiki/Eviction){:target="_blank"}  
[Refresh \| GitHub](https://github.com/ben-manes/caffeine/wiki/Refresh){:target="_blank"}

```java
Cache<String, String> cache = Caffeine.newBuilder()
        .maximumSize(10) // 10件を超えると古いものから取り除かれる。
        .expireAfterWrite(Duration.ofDays(1)) // 書き込みから1日で取り除かれる。
        .refreshAfterWrite(Duration.ofDays(1)) // 書き込みから1日で次回アクセス時に非同期で取り替えられる。
        .expireAfterAccess(Duration.ofHours(1)) // 読み込みが1時間ないと取り除かれる。
        .build();
```

## メトリクス確認
`recordStats`メソッドを指定するとメトリクスが収集される。

```java
Cache<Object, Object> cache = Caffeine.newBuilder().recordStats().build();
System.out.println(cache.stats().toString());

CacheStats{hitCount=0, missCount=0, loadSuccessCount=0, loadFailureCount=0, totalLoadTime=0, evictionCount=0, evictionWeight=0}
```

## Eviction/Removal Listener設定
```java
LoadingCache<String, String> cache = Caffeine.newBuilder()
        .maximumSize(1) // キャッシュの最大サイズ。古いものから取り除かれる。
        .evictionListener((String key, String value, RemovalCause cause) -> {
            System.out.printf("Entry %s -> %s was evicted because of %s\n", key, value, cause);
        })
        .removalListener((String key, String value, RemovalCause cause) -> {
            System.out.printf("Entry %s -> %s was removed because of %s\n", key, value, cause);
        })  
        .build(k -> "value");

cache.get("foo");
cache.get("bar"); // Entry foo -> value was evicted because of SIZE
cache.invalidateAll(); // Entry bar -> value was removed because of EXPLICIT
```
