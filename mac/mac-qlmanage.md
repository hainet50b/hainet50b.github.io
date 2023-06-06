# SVGファイルをPNG形式に変換（qlmanage）

qlmanageはMacのクイックルックを使用するコマンド。  
ファイルの閲覧や編集をコマンドラインから行える。

## SVGファイルをPNG形式に変換
```shell
# -t Compute thumbnails -s Size -o Output dir
qlmanage -t -s 1024 -o /path/to/output-dir target.svg
```

## ファイルをクイックルックで開く
```shell
# -p Preview
qlmanage -p target.svg

# SVGファイルに限らず様々なファイルをプレビューできる。
qlmanage -p target.png
```
