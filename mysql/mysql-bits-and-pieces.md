# MySQL Bits and Pieces

## TIMESTAMP型のサポートする範囲
参考：[MySQL 8.0 リファレンスマニュアル](https://dev.mysql.com/doc/refman/8.0/ja/datetime.html){:target="_blank"}

MySQLのTIMESTAMP型は`1970-01-01 00:00:01`から`2038-01-19 03:14:07`までをサポートする。  
エポック秒の0秒がサポートされていないためデフォルト値に気を付けること。

## デフォルトのディレクトリ構成
Linux: [LinuxへのMySQLのインストール(RPM)](https://dev.mysql.com/doc/refman/8.0/ja/linux-installation-rpm.html){:target="_blank"}  
Mac: [macOSへのMySQLのインストール](https://dev.mysql.com/doc/refman/8.0/ja/macos-installation-pkg.html){:target="_blank"}  
Windows: [Microsoft Windows上でのMySQLのインストールレイアウト](https://dev.mysql.com/doc/refman/8.0/ja/windows-installation-layout.html){:target="_blank"}

Dockerでボリュームをアタッチするデータディレクトリは以下の通り。
```
# Linux
/var/lib/mysql

# Mac
/usr/local/mysql/data

# Windows
$env:PROGRAMDATA¥MySQL¥MySQL Server 8.0
```

## JDBCでMySQLにエラー「No appropriate protocol」で接続できない
JavaのTLSバージョンとMySQL Connector / MySQLのTLSバージョンのデフォルトが異なる場合に発生する。
例えばJava 17とMySQL Connector 8.0.18 / MySQL 5.7の組み合わせではTLSv1.2を指定すると解決する。

```
jdbc:mysql://localhost:3306/pmacho_db?enabledTLSProtocols=TLSv1.2
```
