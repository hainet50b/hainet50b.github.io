# WSLのセットアップ

## WSLをインストールする

参考：[WSLのインストール \| Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/wsl/install){:target="_blank"}  

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

これはMicrosoft Storeで提供されるWSLをアンインストールすると解決する。  
以下のコマンドでバージョンが確認できる場合はMicrosoft Storeで提供されるWSLを使用している。

```powershell
wsl -v

WSL バージョン: 1.2.5.0
カーネル バージョン: 5.15.90.1
WSLg バージョン: 1.0.51
MSRDC バージョン: 1.2.3770
Direct3D バージョン: 1.608.2-61064218
DXCore バージョン: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
Windows バージョン: 10.0.19045.2846
```

「Linux用Windowsサブシステム」を「プログラムと機能」からアンインストールする。

またPowershellを管理者権限で立ち上げて以下のコマンドを発行することでも解消する。  
しかし環境によってはネットワークに不具合が発生する。

```powerhsell
netsh winsock reset

Winsock カタログをリセットしました。
リセットを完了するためにコンピューターを再起動する必要があります。
```
