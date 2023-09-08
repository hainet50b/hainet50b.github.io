# Java GCログ

## Java 11~
```shell
java -XX:+PrintGCDetails -XX:+PrintGCDateStamps -jar pmacho.jar
```

## Java ~8
```shell
java -Xlog:gc*=info::uptime,time,level,tags -jar pmacho.jar
```