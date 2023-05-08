# Mac Bits and Pieces

## uuidgenの出力を小文字にする

```shell
uuidgen | tr '[:upper:]' '[:lower:]'
```

## Apple Musicに取り込んだ曲の場所

m4a形式の実体を確認できる。完全に独立したファイルとして取り扱える。

```
$HOME/Music/Music/Media.localized/Music
```

![mac-bits-and-pieces-f42838d744a1.png](https://programacho.blob.core.windows.net/images/mac-bits-and-pieces-f42838d744a1.png)

## Steam Streaming Microphone/Speakerの削除
```shell
sudo rm /Library/Audio/Plug-Ins/HAL/SteamStreaming*.driver
```
