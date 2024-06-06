# Java - メソッドパラメータの値渡し
{:.no_toc}

* toc
{:toc}

## 基本データ型
Javaは値渡しのため、以下のコードではvalue変数はdoubleItメソッドによる副作用を受けない。

```java
public static void main(String[] args) {
    int value = 1;

    System.out.println(value); // 1
    doubleIt(value);
    System.out.println(value); // 1
}

public static void doubleIt(int value) {
    value *= 2;
    System.out.println(value); // 2
}
```

## 参照型
参照型ではオブジェクト参照そのものが値渡しされるため、valueパラメータの参照を変更してもvalue変数へ影響はない。  
しかし、valueパラメータの操作はオブジェクトへの副作用が発生する。

```java
public static void main(String[] args) {
    List<String> value = new ArrayList<>();
    value.add("foo");

    System.out.println(value); // ["foo"]
    addBar(value);
    System.out.println(value); // ["foo", "bar"]
}

public static void addBar(List<String> value) {
    value.add("bar");
    value = null;
}
```
