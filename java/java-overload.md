# Java - オーバーロード
{:.no_toc}

* toc
{:toc}

## 引数の数に基づくオーバーロード
```java
public static void main(String[] args) {
    method("foo");
    method("foo", "bar");
}

public static void method(String foo) {
}

public static void method(String foo, String bar) {
}
```

## 引数の型に基づくオーバーロード
```java
public static void main(String[] args) {
    method("value");
    method(0);
}

public static void method(String value) {
}

public static void method(Integer value) {
}
```

## 可変長引数を使うオーバーロード
可変長引数を使った曖昧なオーバーロードは避けること。
```java
public static void main(String[] args) {
    method("value"); // 1
    method("foo", new String[]{"bar"}); // 2
    method(new String[] {"foo", "bar"}); // 3
    // method("foo", "bar"); // コンパイルエラー（2と3で曖昧）
}

// 1
public static void method(String value) {
}

// 2
public static void method(String value, String[] values) {
}

// 3
public static void method(String[] values) {
}
```
