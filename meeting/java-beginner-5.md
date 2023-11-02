# Java Beginner #5

## オブジェクト指向
ある概念を状態と操作を持つオブジェクトとして整理して取り扱うこと。  
対象をオブジェクトとして取り扱うことと、それを実現する手段は切り分けて理解する。  
しかし同時に複雑な現実に立ち向かうに当たっては概念と実装、設計とコーディングは不可分でもある。

## ポリモーフィズム
インターフェースは同一であってもオブジェクトごとに動作が異なるという性質のこと。

```java
// 動物は鳴くものであると定義する。
public interface Animal {
    void say();
}

// 犬はワンワンと鳴く。
public class Dog implements Animal {
    @Override
    public void say() {
        System.out.println("ワンワン");
    }
}

// 猫はニャーニャーと鳴く。
public class Cat implements Animal {
    @Override
    public void say() {
        System.out.println("ニャーニャー");
    }
}
```

## 実装と継承
動物は食べて、鳴いて、寝るものであると定義する。  
すると猫も犬も鳥も食べて、鳴いて、寝るものであると言える。  
更にカラスもすずめも食べて、鳴いて、寝るものであると言える。

この概念（＝制約）をプログラミングで表現する手段が実装と継承である。

動物が一般的に何を食べるか、どう鳴くか、どれくらい寝るかは分からない。  
これをJavaではインターフェースで定義する。

```java
public interface Animal {

    // 食べることは分かるが、何を食べるか、
    void eat(String food);

    // 鳴くことは分かるが、どう鳴くか、
    void say();

    // 寝ることは分かるが、どれくらい寝るかは分からない。
    void sleep();
}
```

犬は肉を食べて、ワンワン鳴いて、8時間寝る（とする）。

```java
// 犬は動物として食べて、鳴いて、寝るもので、
public class Dog implements Animal {

    // 肉を食べて、
    @Override
    public void eat(String food) {
        if (food.equals("meat")) {
            System.out.println("食べるワン！");
        } else {
            System.out.println("いらないワン...");
        }
    }

    // ワンワン鳴いて、
    @Override
    public void say() {
        System.out.println("ワンワン");
    }

    // 8時間寝る。
    @Override
    public void sleep() {
        System.out.println("8時間寝るワン");
    }
}
```

猫は魚を食べて、ニャーニャー鳴いて、16時間寝る（とする）。

```java
// 猫は動物として食べて、鳴いて、寝るもので、
public class Cat implements Animal {

    // 魚を食べて、
    @Override
    public void eat(String food) {
        if (food.equals("fish")) {
            System.out.println("食べるニャ！");
        } else {
            System.out.println("いらないニャ...");
        }
    }

    // ニャーニャー鳴いて、
    @Override
    public void say() {
        System.out.println("ニャーニャー");
    }

    // 16時間寝る。
    @Override
    public void sleep() {
        System.out.println("16時間寝るニャ");
    }
}
```

鳥は肉を食べて、チュンチュンと鳴くことは分かるが、どれくらい寝るかは分からない（とする）。  
また犬や猫とは違って飛ぶことができるが、どう飛ぶかは分からない。  
これをJavaでは抽象クラスと抽象メソッドで定義する。

```java
// 鳥は動物として食べて、鳴いて、寝て、さらに飛ぶもので、
public abstract class Bird implements Animal {

    // 肉を食べて
    @Override
    public void eat(String food) {
        if (food.equals("meat")) {
            System.out.println("食べるチュン！");
        } else {
            System.out.println("いらないチュン...");
        }
    }

    // チュンチュン鳴いて、
    @Override
    public void say() {
        System.out.println("チュンチュン");
    }

    // どれくらい寝るかは分からず、

    // 飛ぶことができるが、どう飛ぶかは分からない。
    abstract void fly();
}
```

カラスは肉を食べて、カーカー鳴いて、短時間寝て、ビューンと飛ぶと分かる。  
これをJavaではクラスで定義する。

```java
// カラスは鳥として食べて、鳴いて、寝て、飛ぶもので、
public class Crow extends Bird {

    // 肉を食べて、
    @Override
    public void eat(String food) {
        if (food.equals("meat")) {
            System.out.println("食べるカー！");
        } else {
            System.out.println("いらないカー...");
        }
    }

    // カーカー鳴いて、
    @Override
    public void say() {
        System.out.println("カーカー");
    }

    // 1時間寝て、
    @Override
    public void sleep() {
        System.out.println("1時間寝るカー");
    }

    // ビューンと飛ぶ。
    @Override
    void fly() {
        System.out.println("ビューン！");
    }
}
```

さらにすずめは肉を食べて、チュンチュン鳴いて、短時間寝て、パタパタと飛ぶと分かる。  
これをJavaではクラスで定義し、肉を食べることと、鳴くことは鳥に準拠する。

```java
// すずめは鳥として食べて、鳴いて、寝て、飛ぶもので、
public class Sparrow extends Bird {

    // 肉を食べて、

    // チュンチュン鳴いて、

    // 4時間寝て、
    @Override
    public void sleep() {
        System.out.println("4時間寝るチュン");
    }

    // パタパタと飛ぶ
    @Override
    void fly() {
        System.out.println("パタパタ！");
    }
}
```
