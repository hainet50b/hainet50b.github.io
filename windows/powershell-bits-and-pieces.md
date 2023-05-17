# PowerShell Bits and Pieces

## 以前のセッションのPowerShell履歴を閲覧する
PowerShellの`History`コマンドで閲覧できる履歴は現在のセッションに限られている。  
以前のセッションの履歴は`Get-PSReadlineOption`で取得できるテキストファイルに記録されている。

```powershell
(Get-PSReadlineOption).HistorySavePath
```
