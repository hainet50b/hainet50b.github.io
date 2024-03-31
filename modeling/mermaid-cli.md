# Mermaid CLI
{:.no_toc}

* toc
{:toc}

## ドキュメント
- [mermaid-js/mermaid-cli \| GitHub](https://github.com/mermaid-js/mermaid-cli){:target="_blank"}

## セットアップ
```shell
npm install -g @mermaid-js/mermaid-cli
```

## 使い方
```shell
# mmdファイルをpngファイルとして出力する。
mmdc -i input.mmd -o output.png

# tオプションでテーマを指定できる。
mmdc -i input.mmd -o output.png -t dark

# bオプションで背景透過設定を行える。
mmdc -i input.mmd -o output.png -b transparent
```
