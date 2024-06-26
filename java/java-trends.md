# Javaの動向（サポート・アップデートなど）
{:.no_toc}

* toc
{:toc}

## サポート
押さえておくべきことは以下の3点。
- "Oracle OpenJDK"と"Oracle JDK"は別物である。
- Oracle OpenJDKは次期バージョンがリリースされるまで計2回のアップデートリリースが提供される。
  - とはいえCritical Patch Updatesは継続して提供されており更新状況は随時確認する必要がある。
  - [Critical Patch Updates, Security Alerts and Bulletins](https://www.oracle.com/in/security-alerts/){:target="_blank"}
  - [Welcome to OpenJDK 11 Updates!](https://wiki.openjdk.org/display/JDKUpdates/JDK11u){:target="_blank"}
  - [Welcome to OpenJDK 17 Updates!](https://wiki.openjdk.org/display/JDKUpdates/JDK+17u){:target="_blank"}
  - [Welcome to OpenJDK 21 Updates!](https://wiki.openjdk.org/display/JDKUpdates/JDK+21u){:target="_blank"}
- Oracle JDKは基本的に有償である。
  - ただし、LTS版は次のLTS版がリリースされてから1年後までは無償で使用できる。

参考（Oracle）
- Oracle JDKのバージョンとサポート期限一覧：[Oracle Java SE Supportロードマップ](https://www.oracle.com/jp/java/technologies/java-se-support-roadmap.html){:target="_blank"}
- Oracle系JDKのサポートポリシー：[Oracle Java SE サポート・ロードマップ](https://www.oracle.com/a/ocom/docs/oracle-java-se-jp.pdf){:target="_blank"}

参考（JJUG）
- [JDK: 新しいリリースモデル解説](https://www.slideshare.net/oracle4engineer/jdk-102915437){:target="_blank"}
- [Say hello to Java 17!](https://speakerdeck.com/logico_jp/say-hello-to-java-17){:target="_blank"}
- [Java 21 Overview](https://speakerdeck.com/line_developers/java-21-overview){:target="_blank"}

## ディストリビューション

| ディストリビューション | 提供元 | 公式ページ |
| :-- | :-- | :-- |
| Oracle OpenJDK | Oracle | [https://jdk.java.net/](https://jdk.java.net/){:target="_blank"} |
| Oracle JDK | Oracle | [https://www.oracle.com/java/](https://www.oracle.com/java/){:target="_blank"} |
| Temurin | Adoptium(Eclipse Foundation) | [https://adoptium.net/](https://adoptium.net/){:target="_blank"} |
| Corretto | AWS | [https://aws.amazon.com/jp/corretto/](https://aws.amazon.com/jp/corretto/){:target="_blank"} |
| Microsoft OpenJDK | Microsoft | [https://learn.microsoft.com/ja-jp/java/openjdk/](https://learn.microsoft.com/ja-jp/java/openjdk/){:target="_blank"} |
| Red Hat OpenJDK | Red Hat | [https://developers.redhat.com/products/openjdk/overview](https://developers.redhat.com/products/openjdk/overview){:target="_blank"} |
| Azul Zulu OpenJDK | Azul | [https://www.azul.com/downloads/#zulu](https://www.azul.com/downloads/#zulu){:target="_blank"} |
| Liberica JDK | BellSoft | [https://bell-sw.com/pages/downloads/](https://bell-sw.com/pages/downloads/){:target="_blank"} |

現在使用しているディストリビューションはjavaコマンドで確認できる。

```shell
# Oracle OpenJDK
java -version
openjdk version "21.0.1" 2023-10-17
OpenJDK Runtime Environment (build 21.0.1+12-29)
OpenJDK 64-Bit Server VM (build 21.0.1+12-29, mixed mode, sharing)

# Oracle JDK
java -version
java version "21.0.3" 2024-04-16 LTS
Java(TM) SE Runtime Environment (build 21.0.3+7-LTS-152)
Java HotSpot(TM) 64-Bit Server VM (build 21.0.3+7-LTS-152, mixed mode, sharing

# Temurin
java -version
openjdk version "21.0.3" 2024-04-16 LTS
OpenJDK Runtime Environment Temurin-21.0.3+9 (build 21.0.3+9-LTS)
OpenJDK 64-Bit Server VM Temurin-21.0.3+9 (build 21.0.3+9-LTS, mixed mode, sharing)
```

## アップデート
- [JEP 0: JEP Index](https://openjdk.org/jeps/0){:target="_blank"}
- [Java 11までの変更点](https://hainet50b.github.io/java/java-11){:target="_blank"}
- [Java 17までの変更点](https://hainet50b.github.io/java/java-17){:target="_blank"}

## コミュニティ
日本Javaユーザーグループ（JJUG）のセミナーでトレンドを押さえておくこと。
- 公式ページ：[日本Javaユーザーグループ](https://www.java-users.jp/){:target="_blank"}
- Doorkeeper：[日本Javaユーザーグループ \| Doorkeeper](https://jjug.doorkeeper.jp/){:target="_blank"}
