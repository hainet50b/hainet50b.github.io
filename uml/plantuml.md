# PlantUML
{:.no_toc}

* toc
{:toc}

## ドキュメント
- [PlantUML 概要](https://plantuml.com/en/){:target="_blank"} ([日本語版](https://plantuml.com/ja/){:target="_blank"})
- [PlantUML Web Server](http://www.plantuml.com/plantuml/uml/){:target="_blank"}

## セットアップ
```
# Mac
brew install plantuml

# Windows
scoop install plantuml
```

## 使い方
```shell
plantuml programacho.plantuml

# Windows環境では文字コードを指定しないと文字化けする。
plantuml programacho.plantuml -charset UTF-8

ls
programacho.plantuml	programacho.png
```

## シーケンス図

### 基本
```plantuml
@startuml
Progra -> Macho: Send Protein
Macho --> Progra: Nice Protein!
Macho -> Macho: Macho!!!
@enduml
```

### オブジェクトの種類
```plantuml
@startuml
participant Participant
actor Actor
boundary Boundary
control Control
entity Entity
database Database
collections Collections
queue Queue

Participant -> Actor: To actor 
Participant -> Boundary: To boundary
Participant -> Control: To control
Participant -> Entity: To entity
Participant -> Database: To database
Participant -> Collections: To collections
Participant -> Queue: To queue
@enduml
```

### オブジェクトの順序
```plantuml
@startuml
participant Third order 30
participant Second order 20
participant First order 10
@enduml
```

### オブジェクトの色
- [色の選択 \| PlantUML](https://plantuml.com/en/color){:target="_blank"} ([日本語版](https://plantuml.com/ja/color){:target="_blank"})

```plantuml
@startuml
participant Foo #APPLICATION
participant Bar #Orange
participant Baz #008000
@enduml
```

### 矢印の種類
```plantuml
@startuml
Progra -> Macho /' 通常 '/
Progra ->x Macho /' メッセージの消失 '/
Progra <-> Macho /' 両方向 '/
@enduml
```

### コメントの種類
```plantuml
@startuml
' 一行コメント（シングルクォート not バッククォート）
/'
ブロックコメント
'/
Progra -> Macho /' インラインコメント '/
@enduml
```
