# Java Bits and Pieces
{:.no_toc}

* toc
{:toc}

## JDKのバージョンをコマンドラインから切り替える（Mac）
参考：[jEnv](https://www.jenv.be)

### jEnvのインストール
```shell
brew install jenv

echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(jenv init -)"' >> ~/.zshrc

# jenvコマンドによるJAVA_HOMEの変更を有効にする
jenv enable-plugin export
```

### jEnvへのJDKの登録
```shell
brew list | grep openjdk@

openjdk@11
openjdk@17

# OpenJDK 17を追加
jenv add /opt/homebrew/opt/openjdk@17
openjdk64-17.0.7 added
17.0.7 added
17.0 added
 17.0.7 already present, skip installation

# OpenJDK 11を追加
jenv add /opt/homebrew/opt/openjdk@11
openjdk64-11.0.19 added
11.0.19 added
11.0 added
 11.0.19 already present, skip installation

jenv versions
* system (set by /Users/hainet50b/.jenv/version)
  11.0
  11.0.19
  17.0
  17.0.7
  openjdk64-11.0.19
  openjdk64-17.0.7
```

### JDKの切り替え
```shell
# OpenJDK 17への切り替え
jenv global 17.0.7

jenv global
17.0.7

java -version
openjdk version "17.0.7" 2023-04-18
OpenJDK Runtime Environment Homebrew (build 17.0.7+0)
OpenJDK 64-Bit Server VM Homebrew (build 17.0.7+0, mixed mode, sharing)

mvn -v
Apache Maven 3.9.1 (2e178502fcdbffc201671fb2537d0cb4b4cc58f8)
Maven home: /opt/homebrew/Cellar/maven/3.9.1/libexec
Java version: 17.0.7, vendor: Homebrew, runtime: /opt/homebrew/Cellar/openjdk@17/17.0.7/libexec/openjdk.jdk/Contents/Home
Default locale: ja_JP, platform encoding: UTF-8
OS name: "mac os x", version: "13.2.1", arch: "aarch64", family: "mac"

# OpenJDK 11への切り替え
jenv global 11.0.19

jenv global
11.0.19

java -version
openjdk version "11.0.19" 2023-04-18
OpenJDK Runtime Environment Homebrew (build 11.0.19+0)
OpenJDK 64-Bit Server VM Homebrew (build 11.0.19+0, mixed mode)

mvn -v
Apache Maven 3.9.1 (2e178502fcdbffc201671fb2537d0cb4b4cc58f8)
Maven home: /opt/homebrew/Cellar/maven/3.9.1/libexec
Java version: 11.0.19, vendor: Homebrew, runtime: /opt/homebrew/Cellar/openjdk@11/11.0.19/libexec/openjdk.jdk/Contents/Home
Default locale: ja_JP, platform encoding: UTF-8
OS name: "mac os x", version: "13.2.1", arch: "aarch64", family: "mac"
```

## JDKのバージョンをコマンドラインから切り替える（Windows）
参考：[JEnv-for-Windows](https://github.com/FelixSelter/JEnv-for-Windows)

TODO
