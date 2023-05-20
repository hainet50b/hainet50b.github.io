# juniversalchardet（文字コード判定ライブラリ）

サンプルコード：[juniversalchardet-gym \| GitHub](https://github.com/hainet50b/juniversalchardet-gym){:target="_blank"}  
参考：[juniversalchardet \| GItHub](https://github.com/albfernandez/juniversalchardet){:target="_blank"}

```java
try (InputStream is = Files.newInputStream(Paths.get("UTF-8.txt")) {
    UniversalDetector detector = new UniversalDetector();

    try {
        detector.handleData(is.readAllBytes());
        detector.dataEnd();

        String encoding = detector.getDetectedCharset();
        if (encoding != null) {
            // encoding == "UTF-8"
        } else {
            // No encoding detected
        }
    } finally {
        detector.reset();
    }
} catch (IOException e) {
    throw new RuntimeException(e);
}
```

バイト配列ではなく`Path`を受け取る簡易なインターフェースも存在している。

```java
try {
    String encoding = UniversalDetector.detectCharset(Paths.get("UTF-8.txt"));
    if (encoding != null) {
        // encoding == "UTF-8"
    } else {
        // No encoding detected
    }
} catch (IOException e) {
    throw new RuntimeException(e);
}
```
