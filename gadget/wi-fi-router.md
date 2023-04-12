# Wi-Fiルーターの設定（Wi-Fiルーターの設定（Buffalo WXR-6000AX12S）
[WXR-6000AX12S : Wi-Fiルーター : AirStation | バッファロー](https://www.buffalo.jp/product/detail/wxr-6000ax12s.html){:target="_blank"}

## ルーターモードの自動判別を停止する
ルーターモードの判別による処理のオーバーヘッドを減らすために手動設定する。  
1. AUTO/MANUALスイッチをMANUALに設定する
1. 回線業者から提供された回線終端装置に、
  1. ルーター機能が付いている場合はROUTER/AP/WBスイッチをAPに設定する
  1. ルーター機能が付いていない場合はROUTER/AP/WBスイッチをROUTERに設定する

## 適切なチャンネルを選択する
TODO

## 使用しない電波を止める
必要のない電波を飛ばすことによる処理のオーバーヘッドを減らすために不要な電波を止める。

### 周波数帯（2.4GHz/5GHz）
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「無線LAN」を選択する。  
自宅の機器はすべて5GHz帯に対応しているため2.4GHz帯の電波を無効にする。

### 暗号化方式（WPA2/WPA3）
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「無線LAN」を選択する。  
自宅の機器はすべてWPA3に対応しているためWPA2の電波を無効にする。

![wi-fi-router-7714d08326d8.png](https://programacho.blob.core.windows.net/images/wi-fi-router-7714d08326d8.png)

## 送信出力を下げる
一人暮らしの環境では無駄に強い出力となるため送信出力を下げる。
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「詳細設定」 -> 「無線設定」 -> 「5 GHz (11ax/ac/n/a)」 -> 「送信出力」を75%（例）に設定する。

## メッシュWi-Fi設定を無効化する
一人暮らしの環境ではメッシュWi-Fiは不要なため無効化する。  
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「詳細設定」 -> 「無線設定」 -> 「EasyMesh」 -> 「使用する」のチェックを外す。

## IGNP Snooping機能を無効化する
この機能による接続不安定が多数報告されているため無効化する。状況によっては元に戻す可能性もある。  
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「詳細設定」 -> 「無線設定」 -> 「マルチキャスト制御」 -> 「使用する」のチェックを外す。

## 802.11nプロテクション機能を無効化する
自宅の機器はすべて使用していないため無効化する。  
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。
「詳細設定」 -> 「無線設定」 -> 「5 GHz (11ax/ac/n/a)」 -> 「802.11nプロテクション」 -> 「使用する」のチェックを外す。

## ネット脅威ブロッカーを無効化する
有料の機能で現時点で活用をしていないため無効化する。  
[http://192.168.11.1/cgi-bin/cgi?req=twz](http://192.168.11.1/cgi-bin/cgi?req=twz){:target="_blank"}にアクセスする。  
「詳細設定」 -> 「ネット脅威ブロッカー」 -> 「セキュリティー基本設定コンテンツフィルターPlus」 -> 「使用する」のチェックを外す。
