# DumplingでS3にエクスポートしてAthenaで閲覧する
以下、書きかけ。

## AWS S3にエクスポート
データベースをダンプしてAWS S3にエクスポートすることができる。

```shell
export AWS_ACCESS_KEY_ID=${AccessKey}
export AWS_SECRET_ACCESS_KEY=${SecretKey}

dumpling -h localhost -P 3306 -u root -p changeme \
  -T pmacho_db.emp \
  --no-schemas \
  -o s3://pmacho-bucket/pmacho-app/emp \
  --filetype csv \
  --no-header
```
