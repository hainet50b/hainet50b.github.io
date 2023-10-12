# Java Beginner #1
{:.no_toc}

* toc
{:toc}

## JDKのインストール
パッケージマネージャー（Windows）
```powershell
TODO
```

パッケージマネージャー（Mac）
```shell
brew install openjdk@17
```

## バージョンの確認
```shell
javac -version
javac 17.0.8.1

java -version
openjdk version "17.0.8.1" 2023-08-24
OpenJDK Runtime Environment Homebrew (build 17.0.8.1+0)
OpenJDK 64-Bit Server VM Homebrew (build 17.0.8.1+0, mixed mode, sharing)
```

## javac / java以外のバイナリの確認
```shell
which java
/Users/hainet50b/.jenv/shims/java

ls -la /Users/hainet50b/.jenv/shims/
total 264
drwxr-xr-x@ 35 hainet50b  staff  1120 Sep 26 12:53 .
drwxr-xr-x@ 13 hainet50b  staff   416 May  1 17:06 ..
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jar # Jarの解凍・圧縮
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jarsigner # Jarにデジタル署名を付与
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 java # Javaを起動
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javac # Javaコンパイラ
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javadoc # Javadocを作成
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javap # Javaディスアセンブラ
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jcmd # Javaプロセスの診断情報を収集
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jconsole # JVMのモニタリングツール
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdb # Javaデバッガ
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdeprscan # JavaクラスやJarファイルから非推奨APIの使用を検索
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdeps # Javaの依存関係を調査
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jfr # Java Flight Recorder
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jhsdb # Javaデバッガ
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jimage # Javaモジュールのイメージファイルを検査・操作
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jinfo # Javaプロセスのオプションを調査
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 jjs # JVM上でJavaScriptのコードを実行
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jlink # Javaのランタイムイメージを作成
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jmap # Javaのヒープダンプを出力
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jmod # Javaモジュールのパッケージング・解凍
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jpackage # Javaアプリケーションをネイティブにパッケージング
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jps # Javaプロセスの一覧を表示
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jrunscript # コマンドラインからJVM上でJavaScriptを実行
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jshell # JavaのREPLを提供
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstack # Javaのスレッドダンプを出力
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstat # JVMのGCやヒープの統計情報を取得
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstatd # JVMの監視をするRMIサーバを提供
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 keytool # キーストアを操作
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 pack200 # Jarを圧縮
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 rmic # RMIのスタブを生成
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 rmid # RMIのアクティベーションシステムのデーモン
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 rmiregistry # RMIのリモードオブジェクトを登録
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 serialver # serialVersionUIDの取得・生成
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 unpack200 # pack200で圧縮されたファイルを解凍
```

## 標準入力と標準出力を使用

標準出力
```java
// System.outはPrintStreamインスタンス
// lnはline（改行）を示す。
System.out.println("Java Beginner!");
```

標準入力
```java
// System.inはInputStreamインスタンス
// EOFはCtrl-D（Windowの場合はCtrl-Z）で入力する。
System.in.readAllBytes();
```

## java.lang.System
以下のシステム関連の機能を持つ複数の静的メソッドとフィールドを持っている。
- 標準出力・標準入力
- システムプロパティへのアクセス
- 環境変数へのアクセス
- 時間に関するユーティリティ

## Hello World!
```shell
cat Hello.java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}

javac Hello.java
java Hello
Hello World!
```

## 他のクラスの利用（同一パッケージ）
```shell
tree
├── Hello.java
└── PmachoUtil.java

cat PmachoUtil.java
public class PmachoUtil {
    public static void drink() {
        System.out.println("Drink a protein!");
    }
}

javac PmachoUtil.java

# メインメソッドを実装していないためこれ単体では実行できない。
java PmachoUtil.class
エラー: メイン・クラスPmachoUtil.classを検出およびロードできませんでした
原因: java.lang.ClassNotFoundException: PmachoUtil.class

cat Hello.java
public class Hello {
    public static void main(String[] args) {
        PmachoUtil.drink();
    }
}

javac Hello.java
java Hello
Drink a protein!
```

## 他のクラスの利用（異なるパッケージ）
```shell
tree
├── Hello.java
└── pmacho
    └── PmachoUtil.java

javac pmacho/PmachoUtil.java

# PmachoUtilを見つけられない。
javac Hello.java
Hello.java:6: エラー: シンボルを見つけられません
        PmachoUtil.drink();
        ^
  シンボル:   変数 PmachoUtil
  場所: クラス Hello
エラー1個

# パッケージ宣言を追加する。
cat pmacho/PmachoUtil.java
package pmacho;

public class PmachoUtil {
    public static void drink() {
        System.out.println("Drink a protein!");
    }
}

# インポート宣言を追加する。
cat Hello.java
import pmacho.PmachoUtil;

public class Hello {
    public static void main(String[] args) {
        PmachoUtil.drink();
    }
}

javac pmacho/PmachoUtil.java
javac Hello.java

java Hello
Drink a protein!
```

## 他のクラスの利用（外部Jarライブラリ）
```shell
tree
├── Hello.java
└── jackson
    ├── jackson-annotations-2.15.2.jar
    ├── jackson-core-2.15.2.jar
    └── jackson-databind-2.15.2.jar

cat Hello.java
import com.fasterxml.jackson.databind.ObjectMapper;

public class Hello {
    public static void main(String[] args) {
        new ObjectMapper();
    }
}

# クラスパスを指定しないと適切に利用できない。
javac Hello.java
Hello.java:1: エラー: パッケージcom.fasterxml.jackson.databindは存在しません
import com.fasterxml.jackson.databind.ObjectMapper;
                                     ^
Hello.java:5: エラー: シンボルを見つけられません
        new ObjectMapper();
            ^
  シンボル:   クラス ObjectMapper
  場所: クラス Hello
エラー2個

# クラスパスを指定することで適切に利用できる。
# *でディレクトリ配下のすべてのJarを指定できる。
# :（Windowsでは;）で複数のクラスパスを列挙して指定できる。
# シェルでは一般的に文字列の指定にシングルクォーテーションを使用するが、
# Javaの文脈では環境変数を読み込む場合が多いのでダブルクォーテーションを使用する。
javac -cp "jackson/*" Hello.java
java -cp "./:jackson/*" Hello

# 厳密にJarを指定することもできる。
javac -cp jackson/jackson-core-2.15.2.jar:jackson/jackson-databind-2.15.2.jar:jackson/jackson-annotations-2.15.2.jar Hello.java
java -cp ./:jackson/jackson-core-2.15.2.jar:jackson/jackson-databind-2.15.2.jar:jackson/jackson-annotations-2.15.2.jar Hello
```
