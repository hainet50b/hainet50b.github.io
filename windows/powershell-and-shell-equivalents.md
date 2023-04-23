# PowerShell and Shell Equivalents

## grep / sls - 正規表現によるテキストの検索
```
# Shell
ls | grep hainet50b

# PowerShell
ls | sls hainet50b
```

## lsof / netstat - ポートの使用状況を確認
```
# Shell -n IPアドレスで表示
# Shell -i ネットワークファイルを表示 -n IPアドレスで表示 -P ポート番号を表示
lsof -i -n -P | grep 8080

# PowerShell -a すべてのTCP/UDPポートを表示 -n IPアドレスで表示 -o プロセスIDを表示
netstat -ano | sls 8080
```
