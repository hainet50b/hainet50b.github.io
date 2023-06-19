# JMeter Bits and Pieces

## 起動するJavaバイナリを指定する
JMeterが使用するJavaバイナリはJM_LAUNCH環境変数を参照する。

```
# Mac
JM_LAUNCH=/Users/hainet50b/.jenv/versions/17.0.7/bin/java

# Mac with jEnv
JM_LAUNCH=$(jenv which java)

# Windows
$env:JM_LAUNCH="C:\Users\htakano\scoop\apps\openjdk17\current\bin\java.exe"
```
