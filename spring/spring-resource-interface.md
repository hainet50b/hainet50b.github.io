# Spring Resourceインターフェース
サンプルリポジトリ：[spring-resource-interface | GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-core-gym/spring-resource-interface){:target="_blank"}

## ClassPathResource
```java
try (InputStream in = new ClassPathResource("/pmacho.txt").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```

Resourceインターフェースは`getFile`メソッドで以下のように記述することもできる。
```java
Files.readString(new ClassPathResource("/pmacho.txt").getFile().toPath());
```

しかしファイルがファイルシステム上に存在することを期待しているため、  
IDE上では動作するがJarにパッケージしてデプロイすると意図通りに動作しない。

## UrlResource
```java
try (InputStream in = new UrlResource("https://pages.programacho.com/index.md").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```

## ResourceLoader
ResourceLoaderはDIコンテナから注入して使用する。

```java
try (InputStream in = resourceLoader.getResource("classpath:/pmacho.txt").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```
