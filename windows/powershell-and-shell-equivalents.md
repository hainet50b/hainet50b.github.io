# PowerShell and Shell Equivalents

## grep / sls - 正規表現によるテキストの検索
参考：[Select-String](https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.utility/select-string)
```
# Shell
ls | grep hainet50b

# PowerShell
ls | sls hainet50b
```

## lsof / netstat - ポートの使用状況を確認
参考：[netstat | Microsoft Learn](https://learn.microsoft.com/ja-jp/windows-server/administration/windows-commands/netstat)
```
# Shell -i ネットワークファイルを表示 -n IPアドレスで表示 -P ポート番号を表示
lsof -i -n -P | grep 8080

# PowerShell -a すべてのTCP/UDPポートを表示 -n IPアドレスで表示 -o プロセスIDを表示
netstat -ano | sls 8080
```

## uuidgen / New-Guid - UUIDを生成
```
# Shell
uuidgen

# PowerShell
New-Guid
```