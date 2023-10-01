# Java Beginner

## JDKのインストール
ダウンロード・インストール & パスの指定
```shell
TODO
```

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