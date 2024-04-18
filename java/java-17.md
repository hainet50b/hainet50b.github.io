# Java 17までの変更点
{:.no_toc}

* toc
{:toc}

## 言語仕様

### Record
一連のフィールドを宣言するとアクセッサやコンストラクタが自動生成される。

```java
public void user() {
    User user = new User("hainet50b", 31);

    System.out.println(user.name); // hainet50b
    System.out.println(user.age); // 31
}

public record User(String name, int age) {
}
```

### Switch式
Switchが文ではなく式として取り扱えるようになった。

```java
// Switch文
switch (3) {
    case 1:
        System.out.println("1");
        break;
    case 2:
        System.out.println("2");
        break;
    case 3:
        System.out.println("3");
        break;
    default:
        System.out.println("0");
}

// Switch式
String value = switch (3) {
    case 1 -> "1";
    case 2 -> "2";
    case 3 -> "3";
    default -> {
        System.out.println("0");
        yield "0";
    }
};
```

### instanceof-and-cast
instanceofで型が確定する場合にキャストが必要なくなった。

```java
Object value = "FOO";

if (value instanceof String) {
    ((String) value).toLowerCase(); // foo
}

if (value instanceof String v) {
    v.toLowerCase(); // foo
}
```

### Helpful NullPointerExceptions
NullPointerExceptionが発生した時のメッセージが優しくなった。

```java
String value = null;
value.toLowerCase();

// Java 17(14)より前
// Exception in thread "main" java.lang.NullPointerException

// Java 17(14)以降
// Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.toLowerCase()" because "value" is null
```

### Text Blocks
改行を含む文字列を自然に記述できるようになった。  
ダブルクォーテーションにエスケープが必要ないためJSONを記述しやすくなったことが嬉しい。

```java
String value = """
        {
          "foo": "value"
        }
        """;
```

### Sealed Classes
あるクラス・インターフェースを継承・実装できるクラスを指定できる。現状では使い道が思い浮かばない。

```java
public sealed class SealedClass permits Child {
}

public sealed interface SealedInterface permits Child {
}

public final class Child extends SealedClass implements SealedInterface {
}
```
