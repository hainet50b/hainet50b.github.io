# Java Beginner #7（継承）

オーバーライドしないメソッドは親クラスの値を参照する。

```java
public class Staff {

    public final String id = "Staff-00000";

    public String getId() {
        return this.id;
    }
}

public class Engineer extends Staff {

    public final String id = "Engineer-00000";
}

public class Main {
    public static void main(String[] args) {
        Staff staff = new Staff();
        System.out.println(staff.id); // Staff-00000
        System.out.println(staff.getId()); // Staff-00000

        Engineer engineer = new Engineer();
        System.out.println(engineer.id); // Engineer-00000
        System.out.println(engineer.getId()); // Staff-00000
    }
}
```

オーバーライドしたメソッドは子クラスの値を参照する。

```java
public class Engineer extends Staff {

    public final String id = "Engineer-00000";

    @Override
    public String getId() {
        return this.id;
    }
}

public class Main {
    public static void main(String[] args) {
        Engineer engineer = new Engineer();
        System.out.println(engineer.id); // Engineer-00000
        System.out.println(engineer.getId()); // Engineer-00000
    }
}
```

final修飾子が付与されたクラスを継承するとコンパイルエラーが発生する。

```java
public final class UnextendableClass {
}

public class Child extends UnextendableClass {
}
```

```shell
javac .\Child.java
.\Child.java:1: エラー: final UnextendableClassからは継承できません
class Child extends UnextendableClass {
                    ^
エラー1個
```

final修飾子が付与されたメソッドをオーバーライドするとコンパイルエラーが発生する。

```java
public class ExtendableClass {
    
    public final void hello() {
    }
}

public class Child extends ExtendableClass {

    @Override
    public void hello() {
    }
}
```

```shell
javac .\Child.java
.\Child.java:4: エラー: Childのhello()はExtendableClassのhello()をオー
バーライドできません
    public void hello() {
                ^
  オーバーライドされたメソッドはfinalです
エラー1個
```

継承関係のないクラスではthisの指し示す対象に混乱することはない。

```java
public class Hello {

    private String value;

    public Hello() {
        this("value"); // this("value")はHelloインスタンスのコンストラクタを指す。
    }

    public Hello(String value) {
        this.value = value;
    }

    public void foo() {
        System.out.println(this.value); // this.valueはHelloインスタンスのフィールドを指す。
    }

    public void bar() {
        this.foo(); // this.foo()はHelloインスタンスのメソッドを指す。
    }
}
```

自分自身のコンストラクタを呼ぶと循環参照が発生してコンパイルエラーとなる。

```java
public class Hello {

    public Hello() {
        this();
    }
}
```

```shell
javac .\Hello.java
.\Hello.java:4: エラー: コンストラクタの呼出しが再帰的です
        this();
        ^
エラー1個
```

継承関係のあるクラスでは親のコンストラクタが暗黙的に実行される。

```java
public class Parent {

    public Parent() {
        System.out.println("Parent Constructor");
    }
}

public class Child extends Parent {

    public Child() {
        System.out.println("Child Constructor");
    }
}

public class Main {
    public static void main(String[] args) {
        new Child();
    }
}
// Parent Constructor
// Child Constructor
```

デフォルトコンストラクタ以外を呼ぶ場合はsuperで明示的に指定する。

```java
public class Parent {

    public Parent() {
        System.out.println("Parent Constructor");
    }

    public Parent(String value) {
        System.out.println("Parent Constructor with vars");
    }
}

public class Child extends Parent {

    public Child() {
        System.out.println("Child Constructor");
    }
    
    public Child(String value) {
        System.out.println("Child Constructor with vars");
    }
}

public class Main {
    public static void main(String[] args) {
        new Child("value");
    }
}
// Parent Constructor
// Child Constructor with vars
```

```java
public class Child extends Parent {

    public Child() {
        System.out.println("Child Constructor");
    }

    public Child(String value) {
        super(value);
        System.out.println("Child Constructor with vars");
    }
}

public class Main {
    public static void main(String[] args) {
        new Child("value");
    }
}
// Parent Constructor with vars
// Child Constructor with vars
```

親のメソッドを明示的に呼び出す場合はsuperキーワードを使用する。

```java
public class Parent {

    public void foo() {
        System.out.println("Parent");
    }
}

public class Child extends Parent {

    @Override
    public void foo() {
        System.out.println("Child");
    }

    public void bar() {
        foo();
        super.foo();
    }
}

public class Main {
    public static void main(String[] args) {
        Child child = new Child();
        child.bar();
    }
}
// Child
// Parent
```

フィールドについてもメソッドと同様となる。

```java
public class Parent {

    public String value = "Parent";
}

public class Child extends Parent {

    public String value = "Child";

    public void foo() {
        System.out.println(value);
        System.out.println(super.value);
    }
}

public class Main {
    public static void main(String[] args) {
        Child child = new Child();
        child.foo();
    }
}
// Child
// Parent
```
