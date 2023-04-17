# WSLのセットアップ

## WSLをインストールする
参考：[WSLのインストール | Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/wsl/install){:target="_blank"}  

デフォルトでインストール済みの`wsl`コマンドでインストールする。
```powershell
wsl --install
```

Windowsを再起動するとWSLが立ち上がりUbuntu（デフォルト）にユーザー作成を促される。  
ユーザー名を入力すると以下のエラーメッセージが出力されることがある。
```
実行しようとした操作は、参照したオブジェクトの種類ではサポートされていません。
Error code: Wsl/Service/0x8007273d
```

この場合は別のターミナルでPowershellを管理者権限で立ち上げて以下のコマンドを発行する。
```powerhsell
netsh winsock reset

Winsock カタログをリセットしました。
リセットを完了するためにコンピューターを再起動する必要があります。
```

改めて元のターミナルでユーザー名を入力するとインストールが完了する。
