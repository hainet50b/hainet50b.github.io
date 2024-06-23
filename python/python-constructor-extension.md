# Python - コンストラクタと拡張
{:.no_toc}

* toc
{:toc}

## 暗黙的な呼び出し
スーパークラスのコンストラクタは暗黙的に呼び出される。

```python
class Parent:
    def __init__(self):
        print('Parent')

class Child(Parent):
    pass

Child() # Parent
```

## コンストラクタのオーバーライド
サブクラスでスーパークラスと同じシグネチャのコンストラクタを宣言するとオーバーライドされる。  
スーパークラスのコンストラクタは暗黙的には呼び出されない。

```python
class Parent:
    def __init__(self):
        print('Parent')

class Child(Parent):
    def __init__(self):
        print('Child')

Child() # Child
```

スーパークラスのコンストラクタを呼び出すには明示的に呼び出す。

```python
class Parent:
    def __init__(self):
        print('Parent')

class Child(Parent):
    def __init__(self):
        super().__init__()
        print('Child')

Child()
```

```
Parent # 明示的な呼び出し
Child
```

## メソッドのオーバーライドの副作用
スーパークラスのコンストラクタはサブクラスのオーバーライドしたメソッドを呼び出してしまう。  
コンストラクタではオーバーライドされる恐れのあるメソッド呼び出しを避けた方が良い。

```python
class Parent:
    def __init__(self):
        self.method()

    def method(self):
        print('スーパークラスに固有の動作')

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.method()

    def method(self):
        print('サブクラスに固有の動作')

Child()
```

```
サブクラスに固有の動作 // スーパークラスに固有の動作を期待している。
サブクラスに固有の動作
```
