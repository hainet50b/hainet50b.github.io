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

## S3のデータを確認
```shell
aws s3 ls s3://pmacho-bucket/pmacho-app/emp/
2023-09-20 19:58:38         15 emp.20230920.csv

aws s3 cp s3://pmacho-bucket/pmacho-app/emp/emp.20230920.csv -
1,"hainet50b"
```

## Athenaでテーブルを作成
Athenaテーブル名をDBテーブル名と合わせる。
![dumpling-s3-4aaa339f9708.png](https://programacho.blob.core.windows.net/images/dumpling-s3-4aaa339f9708.png)

Athenaデータベース名をDBデータベース名と合わせる。
![dumpling-s3-5c0e3b575173.png](https://programacho.blob.core.windows.net/images/dumpling-s3-5c0e3b575173.png)

DumplingでエクスポートしたS3バケットを選択する。
![dumpling-s3-72d34dd49cd5.png](https://programacho.blob.core.windows.net/images/dumpling-s3-72d34dd49cd5.png)

データ形式で以下を選択する。
- Apache Hive
- CSV
- org.apache.hadoop.hive.serde2.OpenCSVSerde

separatorCharにカンマを、quoteCharにダブルクォーテーションを指定する。
![dumpling-s3-572db157487e.png](https://programacho.blob.core.windows.net/images/dumpling-s3-572db157487e.png)

列の詳細をDBスキーマに合わせて設定する。
![dumpling-s3-d2cabfdd2cac.png](https://programacho.blob.core.windows.net/images/dumpling-s3-d2cabfdd2cac.png)

## Athenaでテーブルを閲覧
AthenaでSQLインターフェースでデータにアクセスできる。

![dumpling-s3-a2a79aed47e9.png](https://programacho.blob.core.windows.net/images/dumpling-s3-a2a79aed47e9.png)
