# よく使うCLIクライアントのセットアップ
{:.no_toc}

* toc
{:toc}

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
