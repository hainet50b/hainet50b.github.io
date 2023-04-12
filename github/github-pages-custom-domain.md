# GitHub Pagesのカスタムドメイン設定

## リポジトリのカスタムドメイン設定を追加する
リポジトリのSettings -> Pages -> Custom domainでドメインを入力する。
![github-pages-custom-domain-c1e130f4029e.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-c1e130f4029e.png)

Saveをクリックするとリポジトリに以下のような`CNAME`ファイルが追加される。
```
pages.programacho.com
```

## DNSレコードにCNAMEレコードを追加する
以下、ドメインを運用しているお名前.comを前提に記載する。

ドメイン設定 -> ネームサーバーの設定 -> DNS設定/転送設定でドメインを選択して次へをクリックする。
![github-pages-custom-domain-5e7b92e04ea2.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-5e7b92e04ea2.png)

次にDNSレコード設定を利用するの設定するをクリックする。
![github-pages-custom-domain-b559fd8b83fd.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-b559fd8b83fd.png)

A/AAAA/CNAME/MX/NS/TXT/SRV/DS/CAAレコードでCNAMEを入力する。  
`pages.programacho.com`とGitHub Pagesデフォルトドメインの`hainet50b.github.io`を紐付ける。
![github-pages-custom-domain-8c92a1f1fdb9.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-8c92a1f1fdb9.png)

また、DNSレコード設定用ネームサーバー変更確認のチェックを入れてネームサーバーを変更する必要がある。
![github-pages-custom-domain-82e98aee8788.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-82e98aee8788.png)

確認画面へ進むから設定を完了させる。
![github-pages-custom-domain-13b63d89463c.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-13b63d89463c.png)

`dig`コマンドで設定が完了したことを確認する。
```shell
dig pages.programacho.com
```
```
pages.programacho.com.	3600	IN	CNAME	hainet50b.github.io.
```

## HTTPSアクセスを強制する
リポジトリのSettings -> Pages -> Custom domain -> Enforce HTTPSにチェックを入れてHTTPSアクセスを強制する。
DNSレコード設定を行ってからおおよそ一晩ほど経つまでドメインの検証に成功しなかった。
![github-pages-custom-domain-75908e1a82f6.png](https://programacho.blob.core.windows.net/images/github-pages-custom-domain-75908e1a82f6.png)
