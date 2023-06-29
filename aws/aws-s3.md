# AWS S3
{:.no_toc}

* toc
{:toc}

## バケット
```shell
# バケット一覧を確認
aws s3 ls

# バケットを作成 
aws s3 mb s3://pmacho-bucket
make_bucket: pmacho-bucket

# バケットを削除
# バケットが空でなければ削除できない。
aws s3 rb s3://pmacho-bucket
remove_bucket failed: s3://pmacho-bucket An error occurred (BucketNotEmpty) when calling the DeleteBucket operation: The bucket you tried to delete is not empty

aws s3 rm --recursive s3://pmacho-bucket
delete: s3://pmacho-bucket/pmacho.json
...

aws s3 rb s3://pmacho-bucket
remove_bucket: pmacho-bucket
```

## ファイル
```shell
# ファイル一覧を確認
aws s3 ls s3://pmacho-bucket
2023-06-29 16:38:43 52 pmacho.json

# ディレクトリやファイルを前方一致で指定できる。
aws s3 ls s3://pmacho-bucket/b
2023-06-28 14:57:50 52 bar.json
2023-06-28 14:57:55 52 baz.json

# ファイルの内容を確認
aws s3 cp s3://pmacho-bucket/pmacho.json -
{"foo": "value1", "bar": "value2", "baz": "balue3"}

# ファイルを転送
aws s3 cp pmacho.json s3://pmacho-bucket
upload: ./pmacho.json to s3://pmacho-bucket/pmacho.json

# ファイルを削除
aws s3 rm s3://pmacho-bucket/pmacho.json
delete: s3://pmacho-bucket/pmacho.json
```
