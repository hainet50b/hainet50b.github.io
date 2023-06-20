# 文字コードの確認・変更（nkf）
公式ページ：[nkf Network Kanji Filter](https://ja.osdn.net/projects/nkf/){:target="_blank"}

## nkfのインストール
```
# Mac
brew install nkf

# Windows
scoop bucket add jp https://github.com/dooteeen/scoop-for-jp
scoop install nkf
```

## ファイルの文字コード確認
```shell
# -g (--guess)
nkf -g input.txt
UTF-8
```

## ファイルの文字コード変更
```shell
# to UTF-8
nkf -w input.txt > output.txt

# to Shift-JIS
nkf -s input.txt > output.txt
```

## （オプション）UTF-8 / Shift-JISのファイル作成
```shell
echo あいうえお | nkf -s > output.txt

nkf -g output.txt
Shift-JIS
```
