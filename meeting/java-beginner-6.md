# Java Beginner #6（クラス定義・オブジェクト生成）

## クラス定義
属性（＝フィールド）と操作（＝メソッド）を含むクラスを定義できる。

```java
public class Hello {
}
```

クラスは一つのJavaファイルに複数定義できるが、以下の制約がある。
- publicクラスはJavaファイルに一つしか定義できない。
- publicクラスはJavaファイル名と一致していなければならない。

publicでないクラスは通常あまり使用しないため事実上クラスとファイルは一対一対応となっている。

複数のクラスを定義したJavaファイルをコンパイルすると複数のクラスファイルが出力される。

```shell
cat Hello.java
class Hello1 {}
class Hello2 {}

java Hello.java
ls -la
-rw-r--r--@  1 hainet50b  staff    33 Nov  8 20:03 Hello.java
-rw-r--r--@  1 hainet50b  staff   185 Nov  8 20:03 Hello1.class
-rw-r--r--@  1 hainet50b  staff   185 Nov  8 20:03 Hello2.class
```

## メンバ定義
クラスに含まれるフィールドとメソッドはメンバと呼ばれる。  
フィールドはインスタンスごとに値が異なり、メソッドはインスタンスを指定して実行する。

フィールド定義

```java
public class Hello {
    public String value;
}
```

メソッド定義  
メソッドの定義は関数（n -> m）を意識すると良い。  
引数とメソッド名はそのメソッドのインターフェースを構成するが、これをシグネチャと呼ぶ。

```java
public class Hello {
    public void hello() {}
}
```

## インスタンス化
newキーワードでクラスからインスタンスを生成できる。  
インスタンスごとにフィールドは状態を持ち、インスタンスを指定してメソッドを実行する。

```java
new Hello();
```

通常、生成したインスタンスは変数に格納して参照を保持する。  
変数に格納せずとも生成した時点でヒープの領域を確保している。

```java
Hello hello = new Hello();
```

## コンストラクタ
インスタンス生成時に任意の動作を実行することができる。  
通常はインスタンスの初期状態を決定するために使用し、必要以上の動作を実行させない。

コンストラクタで外部リソース（データベースなど）にアクセスするなどの重い処理は記述するべきではない。

```java
public class Hello {
    public String value;

    public Hello(String value) {
        this.value = value;
    }
}
```

コンストラクタを定義しない場合は、引数を受け付けず動作を一切実行しないデフォルトコンストラクタが起動する。

```java
public class Hello {
    // デフォルトコンストラクタのイメージ
    public Hello() {}
}
```

## オーバーロード
シグネチャが同じメソッドは同時に複数定義することができないが、少しでも違っていれば定義することができる。

```java
// メソッド名がhelloで、引数がないメソッド
public String hello() {};
// メソッド名がhelloで、Stringを一つ受け付けるメソッド
public STring hello(String foo) {};
// メソッド名がhelloで、Stringを二つ受け付けるメソッド
public STring hello(String foo, String bar) {};
```

一方で返り値の型はシグネチャに含まれないため、異なっていても定義できない。  
利用者の直感としてもどちらが実行されるか判別できない。

```java
// 以下はコンパイルエラーとなる。
public String hello() {};
public void hello() {};
```

コンストラクタもオーバーロードすることができる。  
ユーザーにインスタンス生成手段を複数提供する使い道が多い。

```java
public class Hello {
    public Hello() {}
    public Hello(String value) {}
}
```

## staticメンバの定義
インスタンスではなくクラスに所属するメンバをstaticメンバという。

```java
public class Hello {
    public static String value;
    public static void hello() {};
}
```

インスタンスではなくクラスに所属するため、呼び出しはクラスに対して行う。

```java
Hello.value = "foo";
Hello.hello();
```

staticメソッドはインスタンス変数を参照できない。

```shell
cat Hello.java
public class Hello {
    public String value;

    public static void hello() {
        String value = this.value;
    }
}

javac Hello.java
Hello.java:5: エラー: staticでない変数 thisをstaticコンテキストから参照することはできません
String value = this.value;
^
エラー1個
```

同様にしてstaticメソッドはインスタンスメソッドを参照できない。

```shell
cat Hello.java
public class Hello {
    public static void hello() {
        hello2();
    }

    public void hello2() {}
}

javac Hello.java
Hello.java:3: エラー: staticでないメソッド hello2()をstaticコンテキストから参照することはできません
hello2();
^
エラー1個
```

## アクセス修飾子
クラス、コンストラクタ、フィールド、メソッドにそれぞれ付与してアクセスを制限できる。  
オブジェクトの要件を満たす範囲で厳しく設定することが望ましい。
