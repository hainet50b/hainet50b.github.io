# DumplingでダンプをS3にエクスポートしてAthenaで閲覧する
以下、書きかけ。

## AWSアクセスキーの設定
アクセスキーは環境変数を参照する。

```shell
export AWS_ACCESS_KEY_ID=${AccessKey}
export AWS_SECRET_ACCESS_KEY=${SecretKey}
```

## AWS S3にエクスポート
```shell
# --no-schemas データファイルのみダンプする
# -o S3 URIを指定する
# --filetype AWS Athenaで閲覧するためCSV形式でダンプする --no-header ヘッダー行を取り除く
dumpling -h localhost -P 3306 -u root -p changeme \
  -T pmacho_db.emp \
  --no-schemas \
  -o s3://pmacho-bucket/pmacho-app/emp \
  --output-filename-template "emp.$(date "+%Y%m%d")" \
  --filetype csv \
  --no-header
```

## metadataを削除
ダンプ結果には不要なmetadataファイルが含まれているため削除する。

```shell
aws s3 rm s3://pmacho-bucket/pmacho-app/emp/metadata
```
