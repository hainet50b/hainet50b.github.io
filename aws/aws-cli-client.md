# AWS CLIクライアント

## AWS CLIクライアントのインストール
```shell
# Mac
brew install awscli

# Windows
scoop install aws
```

## AWSのログイン設定
アクセスキーは管理コンソール右上のユーザー名 > セキュリティ認証情報 > アクセスキーから作成する。

```shell
aws configure

AWS Access Key ID [None]: ***
AWS Secret Access Key [None]: ***
Default region name [None]: ap-northeast-1
Default output format [None]: json

aws configure list

      Name                    Value                    Type Location
      ----                    -----                    ---- --------
   profile                <not set>                    None None
access_key     ******************** shared-credentials-file
secret_key     ******************** shared-credentials-file
    region           ap-northeast-1             config-file ~/.aws/config
```

## 出力フォーマットの設定
デフォルトフォーマットはJSON、コマンドフォーマットはテキストにすることが多い。

```shell
# JSON形式
aws configure set default.output json

# テキスト形式
aws configure set default.output text

# テーブル形式
aws configure set default.output table
```
