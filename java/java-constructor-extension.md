# Java - コンストラクタと拡張
{:.no_toc}

* toc
{:toc}

## 暗黙的な呼び出し
スーパークラスのコンストラクタは暗黙的に呼び出される。

```java
public class Programacho {
    public static void main(String[] args) {
        new Child();
    }
}

class Parent {
    public Parent() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    public Child() {
        System.out.println("Child");
    }
}
```

```
Parent // 暗黙的に呼び出されている。
Child
```

## オーバーライドのコンストラクタへの副作用
スーパークラスのコンストラクタはサブクラスのオーバーライドしたメソッドを呼び出してしまう。  
コンストラクタではオーバーライド可能なメソッド（privateでもstaticでもfinalでもない）の呼び出しを避けた方が良い。

```java
public class Programacho {
    public static void main(String[] args) {
        new Child();
    }
}

class Parent {
    public Parent() {
        action();
    }

    public void action() {
        System.out.println("スーパークラスに固有の動作");
    }
}

class Child extends Parent {
    public Child() {
        action();
    }

    @Override
    public void action() {
        System.out.println("サブクラスに固有の動作");
    }
}
```

```
サブクラスに固有の動作 // スーパークラスに固有の動作を期待している。
サブクラスに固有の動作
```
