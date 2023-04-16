# WSLのセットアップ

## WSLをインストールする
[WSLのインストール](https://learn.microsoft.com/ja-jp/windows/wsl/install)
初期状態ですでに`wsl`コマンドがインストールされている。
```powershell
wsl --install
```

インストールコマンドを発行したあとWindowsを再起動してWSLを起動する。
```powershell
wsl
```

Linuxが起動するとユーザー作成を促されるが、ユーザー名を入力すると以下のエラーメッセージが出力される。
```
実行しようとした操作は、参照したオブジェクトの種類ではサポートされていません。
Error code: Wsl/Service/0x8007273d
```

別のターミナルでPowershellを管理者権限で立ち上げて以下のコマンドを発行する。
```powerhsell
netsh winsock reset

Winsock カタログをリセットしました。
リセットを完了するためにコンピューターを再起動する必要があります。
```

改めて元のターミナルでユーザー名を入力するとセットアップが完了する。
