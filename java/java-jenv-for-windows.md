# JDKをコマンドラインから切り替える（JEnv for Windows）

参考：[JEnv-for-Windows](https://github.com/FelixSelter/JEnv-for-Windows){:target="_blank"}

## JEnv for Windowsのインストール

```powershell
scoop bucket add extras
scoop install jenv
```

## JEnv for WindowsへのJDKの登録

```powershell
scoop list openjdk | Select-Object Name, Version

Name            Version
----            -------
openjdk11       11.0.2-9
openjdk17       17.0.2-8

where java

C:\Users\hainet50b\scoop\apps\openjdk17\current\bin\java.exe
C:\Users\hainet50b\scoop\apps\openjdk11\current\bin\java.exe

# JDKを登録
jenv add openjdk17 C:\Users\hainet50b\scoop\apps\openjdk17\current
jenv add openjdk11 C:\Users\hainet50b\scoop\apps\openjdk11\current

# JDKの一覧を表示
jenv list

name            path
----            ----
openjdk17       C:\Users\hainet50b\scoop\apps\openjdk17\current
openjdk11       C:\Users\hainet50b\scoop\apps\openjdk11\current
```

## JDKの切り替え（例：17 -> 11）
```powershell
jenv change openjdk11

java -version
openjdk version "11.0.2" 2019-01-15
OpenJDK Runtime Environment 18.9 (build 11.0.2+9)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.2+9, mixed mode)

# JAVA_HOMEはセッションを再読み込みしないと更新されない。
echo $env:JAVA_HOME
C:\Users\htakano\scoop\apps\openjdk17\current

# ...再読み込み
echo $env:JAVA_HOME
C:\Users\htakano\scoop\apps\openjdk11\current

# （オプション）JDKの切り替え（セッションスコープ）
jenv use openjdk11

JEnv changed for the current shell session. Careful this overwrites "jenv local"
```
