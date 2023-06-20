# 改行コードの確認・変更（dos2unix / unix2dos）

## dos2unix / unix2dosのインストール
```
# Mac
brew install dos2unix

# Windows
scoop install file
scoop install dos2unix
```

## ファイルの改行コード確認
```shell
file input.txt

# LFの場合
input.txt: ASCII text

# CRLFの場合
input.txt: ASCII text, with CRLF line terminators
```

## ファイルの改行コード変更
```shell
# CRLF -> LF
dos2unix input.txt

# LF -> CRLF
unix2dos input.txt
```

## （オプション）LF / CRLFのファイル作成
```shell
# LF
echo "First\nSecond" > input.txt

# CRLF
echo "First\nSecond" | unix2dos > input.txt
```
