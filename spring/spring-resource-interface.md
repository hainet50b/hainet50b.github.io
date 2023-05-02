# Spring Resource Interface
サンプルリポジトリ：[spring-resource-interface | GitHub](https://github.com/hainet50b/spring-gym/tree/main/spring-resource-interface){:target="_blank"}

## ClassPathResource
```java
try (InputStream in = new ClassPathResource("/foo.txt").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```

Resourceインターフェースは`getFile`メソッドを持っているため以下のように記述することもできる。
```java
Files.readString(new ClassPathResource("/foo.txt").getFile().toPath());
```

しかし対象がファイルシステム上に存在することを期待しているため、  
IDE上では動作するがJarにパッケージしてデプロイすると意図通りに動作しない。

## UrlResource
```java
try (InputStream in = new UrlResource("https://pages.programacho.com/index.md").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```

## ResourceLoader
```java
try (InputStream in = resourceLoader.getResource("classpath:/foo.txt").getInputStream()) {
    StreamUtils.copyToString(in, Charset.defaultCharset());
}
```
