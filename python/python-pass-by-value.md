# Python - メソッドパラメータの値渡し
{:.no_toc}

* toc
{:toc}

## イミュータブルオブジェクト
Pythonは値渡しのため、以下のコードではvalue変数はdoubleIt関数による副作用を受けない。

```python
value = 1

def doubleIt(value):
    value *= 2
    print(value) # 2

print(value) # 1
doubleIt(value)
print(value) # 1
```

## ミュータブルオブジェクト
ミュータブルオブジェクトではオブジェクト参照そのものが値渡しされるため、valueパラメータの参照を削除してもvalue変数へ影響はない。  
しかし、valueパラメータの操作はオブジェクトへの副作用が発生する。

```python
value = ['foo', 'bar']

def appendBaz(value):
    value.append('baz')
    del value

print(value) # ['foo', 'bar']
appendBaz(value)
print(value) # ['foo', 'bar', 'baz']
```
