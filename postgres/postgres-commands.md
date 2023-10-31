# Postgres よく使うコマンド
{:.no_toc}

* toc
{:toc}

## クライアントインストール

### Mac
[libpq — Homebrew Formulae](https://formulae.brew.sh/formula/libpq){:target="_blank"}

```shell
brew install libpq
```

### Windows
[postgresql \| Scoop Apps](https://bjansen.github.io/scoop-apps/main/postgresql/){:target="_blank"}

```powershell
# PostgreSQLサーバーも同時にインストールされてしまう。
scoop install postgresql
```

## ログイン
```shell
psql -h localhost -p 5432 -U postgres -W
```
