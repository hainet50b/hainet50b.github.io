# jarコマンドでJarの中身を差し替える

{:.no_toc}

* toc
{:toc}

## Jarを展開
```shell
# -x (--extract) 展開 -v (--verbose) 詳細を出力 -f (--file) ファイル名
jar -xvf programacho.jar

# Spring Bootアプリケーションを展開すると以下のようになる。
tree -L 2 .
.
├── BOOT-INF
│   ├── classes
│   ├── classpath.idx
│   ├── layers.idx
│   └── lib
├── META-INF
│   ├── MANIFEST.MF
│   └── maven
├── programacho.jar
└── org
└── springframework
```

## ファイルを指定してJarから抽出
```shell
jar -xvf programacho.jar BOOT-INF/classes/application.yml

tree .
.
├── BOOT-INF
│   └── classes
│       └── application.yml
└── programacho.jar
```

## ファイルを指定してJarへ梱包
`application.yml`などの設定ファイルを差し替えられる。  
ソースコードが存在しないJarファイルを取り扱う場合に有効。

```shell
# -u (--update) 更新
jar -uvf programacho.jar BOOT-INF/classes/application.yml
```
