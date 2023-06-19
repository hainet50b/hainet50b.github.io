# JDKをコマンドラインから切り替える（jEnv）

参考：[jEnv](https://www.jenv.be){:target="_blank"}

## jEnvのインストール

```shell
brew install jenv

echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(jenv init -)"' >> ~/.zshrc

# jenvコマンドによるJAVA_HOMEの変更を有効にする。
jenv enable-plugin export
```

## jEnvへのJDKの登録

```shell
brew list | grep openjdk@

openjdk@11
openjdk@17

# JDKを登録
jenv add /opt/homebrew/opt/openjdk@17
jenv add /opt/homebrew/opt/openjdk@11

# JDKの一覧を表示
jenv versions

* system (set by /Users/hainet50b/.jenv/version)
  11.0
  11.0.19
  17.0
  17.0.7
  openjdk64-11.0.19
  openjdk64-17.0.7
```

## 登録したJavaのパスの確認
```shell
jenv which java
/Users/hainet50b/.jenv/versions/17.0.7/bin/java

# jEnvを経由するため直接Javaのパスを参照できない
which java
/Users/hainet50b/.jenv/shims/java
```

## JDKの切り替え

```shell
# JDKの切り替え（例：17 -> 11）
jenv global 11.0.19

jenv global
11.0.19

java -version
openjdk version "11.0.19" 2023-04-18
OpenJDK Runtime Environment Homebrew (build 11.0.19+0)
OpenJDK 64-Bit Server VM Homebrew (build 11.0.19+0, mixed mode)

echo $JAVA_HOME
/Users/hainet50b/.jenv/versions/11.0.19

# （オプション）JDKの切り替え（セッションスコープ）
jenv shell 11.0.19

jenv shell
11.0.19

# （オプション）JDKの切り替え（ディレクトリスコープ）
jenv local 11.0.19

jenv local
11.0.19

# .java-versionが作成されて永続的にJDKバージョンが切り替わる。
ls -la
-rw-r--r--@  1 hainet50b  staff    8 Jun 11 22:27 .java-version
```
