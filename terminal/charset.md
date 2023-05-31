# 文字コードの確認・変更（file / iconv）

## ファイルの文字コード確認
```shell
file input.txt
index.md: Unicode text, UTF-8 text
```

## ファイルの文字コード変更
```shell
# サポートされている文字コードの確認
iconv -l

# Shift-JIS to UTF-8
iconv -f Shift-JIS -t UTF-8 input.txt > output.txt

# UTF-8 to Shift-JIS
iconv -f UTF-8 -t Shift-JIS input.txt > output.txt
```

## （オプション）UTF-8 / Shift-JISのファイル作成
```
echo あいうえお | iconv -t Shift-JIS > output.txt

file output.txt
output.txt: Non-ISO extended-ASCII text
```
