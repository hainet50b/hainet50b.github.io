# PowerShell and Shell Equivalents
{:.no_toc}

* toc
{:toc}

## grep / sls - 正規表現によるテキストの検索

参考：[Select-String | Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.utility/select-string){:target="_blank"}  

```
# Shell
ls | grep hainet50b

# PowerShell
ls | Out-String -Stream | sls hainet50b
```

## カレントディレクトリ配下をオブジェクトのリストではなく文字列として出力

参考：[Out-String | Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.utility/out-string){:target="_blank"}

```powershell
ls | Out-String -Stream
```

以下のようにして関数として定義しておくと便利。

```powershell
function ll { ls | Out-String -Stream }
```

## lsof / netstat - ポートの使用状況を確認

参考：[netstat | Microsoft Learn](https://learn.microsoft.com/ja-jp/windows-server/administration/windows-commands/netstat){:target="_blank"}

```
# Shell -i ネットワークファイルを表示 -n IPアドレスで表示 -P ポート番号を表示
lsof -i -n -P | grep 8080

# PowerShell -a すべてのTCP/UDPポートを表示 -n IPアドレスで表示 -o プロセスIDを表示
netstat -ano | sls 8080

# プロセス名まで表示する場合は-bオプションを付与する。
# 出力に改行を含むためポート番号での絞り込みが難しい。
netstat -anob
```

## uuidgen / New-Guid - UUIDを生成

参考：[New-Guid | Microsoft Learn](https://learn.microsoft.com/ja-jp/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.3){:target="_blank"}

```
# Shell
uuidgen

# PowerShell
New-Guid
```