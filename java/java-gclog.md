# Java GCログ

## Java 11~
```shell
java -XX:+PrintGCDetails -XX:+PrintGCDateStamps -jar pmacho.jar
```

## Java ~8
```shell
java -Xlog:gc*=info::uptime,time,level,tags -jar pmacho.jar
```

## GCViewer（GCログ解析ツール）
[GCViewer \| GitHub](https://github.com/chewiebug/GCViewer){:target="_blank"}

### インストール方法
```shell
# Mac
brew install gcviewer

# Windows
Scoopにgcviewerのパッケージがないため公式ページよりダウンロードして使用する。
```
