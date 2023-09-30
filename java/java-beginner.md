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
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jar
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jarsigner
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 java
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javac
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javadoc
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 javap
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jcmd
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jconsole
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdb
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdeprscan
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jdeps
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jfr
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jhsdb
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jimage
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jinfo
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 jjs
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jlink
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jmap
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jmod
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jpackage
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jps
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jrunscript
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jshell
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstack
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstat
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 jstatd
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 keytool
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 pack200
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 rmic
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 rmid
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 rmiregistry
-rwxr-xr-x@ 28 hainet50b  staff   418 May  1 16:57 serialver
-rwxr-xr-x@  5 hainet50b  staff   418 May  1 16:57 unpack200
```