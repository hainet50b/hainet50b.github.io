# よく使うCLIクライアント設定
{:.no_toc}

* toc
{:toc}

## MySQL

### Mac
[mysql-client — Homebrew Formulae](https://formulae.brew.sh/formula/mysql-client){:target="_blank"}
```shell
brew install mysql-client
```

### Windows
Scoopでインストールする場合は同時にサーバーもインストールされてしまう。  
[Mysql](https://bjansen.github.io/scoop-apps/main/mysql/){:target="_blank"}
```powershell
scoop install mysql
```

### Getting Started
```shell
mysql -h localhost -P 3306 -u root -p
```

## SQL*Plus

### Mac
[InstantClientTap/homebrew-instantclient: A tap of Mac Homebrew formulas for Oracle Instant Client.](https://github.com/InstantClientTap/homebrew-instantclient){:target="_blank"}
```shell
brew tap InstantClientTap/instantclient
brew install instantclient-sqlplus
```

### Windows
[Oracle Instant Client Sqlplus](https://bjansen.github.io/scoop-apps/main/oracle-instant-client-sqlplus/){:target="_blank"}
```powershell
scoop install oracle-instant-client-sqlplus
```

### Getting Started
```shell
sqlplus pdbadmin/changeme@//localhost:1521/XEPDB1
```

## Redis

### Mac
同時にサーバーもインストールされてしまう。  
[redis — Homebrew Formulae](https://formulae.brew.sh/formula/redis){:target="_blank"}
```shell
brew install redis
```

### Windows
同時にサーバーもインストールされてしまう。  
[Redis](https://bjansen.github.io/scoop-apps/main/redis/){:target="_blank"}
```powershell
scoop install redis
```

### Getting Started
```shell
redis-cli
```
