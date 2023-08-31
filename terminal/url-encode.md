# URLエンコード・デコード（nkf）

```shell
# URLエンコード -We UTF8 -> UTF8 -MQ MIME Encode Quoted tr =を%に置換
echo プログラマッチョ | nkf -WwMQ | tr = %
%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9E%E3%83%83%E3%83%81%E3%83%A7

# URLデコード
value='%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9E%E3%83%83%E3%83%81%E3%83%A7'
echo $value | nkf -Ww --url-input
プログラマッチョ
```
