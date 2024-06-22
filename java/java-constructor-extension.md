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
        System.out.println("Xに固有の動作");
    }
}

class Child extends Parent {
    public Child() {
        action();
    }

    @Override
    public void action() {
        System.out.println("Yに固有の動作");
    }
}
```

```
Yに固有の動作 // Xに固有の動作を期待している。
Yに固有の動作
```
