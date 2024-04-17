# Java 11までの変更点
{:.no_toc}

* toc
{:toc}

## 言語仕様

### varによる型推論の導入
ローカル変数で型推論ができるようになった。  
現時点では、明確な意図がない場合は使用しない方が良いと考えている。

```java
var value = "foo";
```

### インターフェースでのprivateメソッドの宣言
インターフェースにprivateメソッドを宣言できるようになった。  
defaultメソッドで使用することを想定している。

```java
public interface PrivateMethodInInterface {

    default void foo() {
        privateMethod();
    }

    default void bar() {
        privateMethod();
    }

    private void privateMethod() {
    }
}
```

### 匿名クラスでのダイヤモンド演算子の使用
```java
List<String> values = new ArrayList<>() {{
    this.add("foo");
    this.add("bar");
}};
```

## API

### HttpClient
新たにHttpClientインターフェースが追加された。HttpURLConnectionと比較して大幅に読みやすくなっている。

```java
HttpClient httpClient = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder(URI.create("http://example.com")).build();

HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
```

### String
```java
// repeat
"foo".repeat(3); // foofoofoo

// isBlank
"".isBlank(); // true
"foo".isBlank(); // false

// strip
// trimと比較して取り扱うコード・ポイントが広くなり、例えば全角スペースも対象となった。
"　".trim(); // "　"
"　".strip(); // ""

// lines
"foo\nbar\nbaz".lines().collect(Collectors.toList()); // [foo, bar, baz]
```

### コレクション（List/Set/Map）
```java
// of
// イミュータブルなコレクションを返却する。
List.of("foo", "bar");
Set.of("foo", "bar");
Map.of(
    "foo", "value1",
    "bar", "value2"
);

// Map#ofEntries
Map.ofEntries(
    entry("foo", "value1"),
    entry("bar", "value2")
);

// copyOf
// ofと同様にイミュータブルなコレクションを返却する。
List<String> values = new ArrayList<>() {{
    this.add("foo");
    this.add("bar");
}};
List.copyOf().add("baz"); // UnsupportedOperationException

// toArray
List.of("foo", "bar").toArray(String[]::new)[1]; // bar
```

### Stream
```java
// ofNullable
Stream.of(null); // NullPointerException
Stream.ofNullable(null) // empty

// dropWhile / takeWhile
IntStream.of(1, 2, 3, 4, 5)
        .dropWhile(i -> !(i == 2))
        .takeWhile(i -> !(i == 5))
        .sum(); // 9

// iterate
// foriと同様にi++とするとラムダ式のreturn後に加算処理が行われるため終了しなくなる。
IntStream.iterate(0, i < 10, i -> i + 3).sum(); // 18
```

### Collectors
```java
// toUnmodifiableList / toUnmodifiableSet / toUnmodifiableMap
Stream.of("foo").collect(Collectors.toUnmodifiableList()).add("bar"); // UnsupportedOperationException

// filtering
Stream.of("foo", "bar").collect(Collectors.filtering(
        v -> v.equals("foo"), 
        Collectors.toList()
)); // [foo]

// flatMapping
Stream.of("foo").collect(Collectors.flatMapping(
        Stream::of, 
        Collectors.toList()
)); // [foo]
```

### Optional
```java
// orElseThrow
Optional.empty().orElseThrow(() -> new RuntimeException()); // RuntimeException

// ifPresentOrElse
Optional.of("foo").ifPresentOrElse(
        System.out::println,
        () -> {
            throw new RuntimeException();
        }
); // foo
Optional.empty().ifPresentOrElse(
        System.out::println,
        () -> {
            throw new RuntimeException();
        }
); // RuntimeException

// or
Optional.of("foo").or(() -> Optional.of("bar")); // foo
Optional.empry().or(() -> Optional.of("bar")); // bar

// stream
// emptyをフィルタリングする手間を省くことができるようになった。
Stream<User> users = userRepository.findAll().stream();
// Java 11以降
users
        .flatMap(user -> user.getAge().stream())
        .collect(Collectors.toList());
// 従来
users
        .map(User::getAge)
        .filter(Optional::isPresent)
        .collect(Collectors.toList());
```

### Predicate
```java
// not
not(b -> (boolean) b).test(true); // false
// !演算子と異なり、メソッド参照に適用できる。
Stream.of("", "foo", "bar").filter(not(String::isEmpty)).collect(Collectors.toList()); // [foo, bar]
```

## 機能

### JShell
JavaにおけるREPLに該当するJShellが追加された。

```jshell
❯ jshell
|  JShellへようこそ -- バージョン21.0.1
|  概要については、次を入力してください: /help intro

jshell> var value = "foo"
value ==> "foo"

jshell> System.out.println(value)
foo

jshell> /exit
|  終了します
```

### 単一Javaファイルのjavaコマンドによる直接実行
```shell
echo 'public class Main { public static void main(String[] args) { System.out.println("foo"); } }' > Main.java
java ./Main.java
foo
```