# MySQL Bits and Pieces

## TIMESTAMP型のサポートする範囲
参考：[MySQL 8.0 リファレンスマニュアル](https://dev.mysql.com/doc/refman/8.0/ja/datetime.html){:target="_blank"}

MySQLのTIMESTAMP型は`1970-01-01 00:00:01`から`2038-01-19 03:14:07`までをサポートする。  
エポック秒の0秒がサポートされていないためデフォルト値に気を付けること。

## デフォルトのディレクトリ構成
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