# Elasticsearch インデックス／ドキュメントAPI
{:.no_toc}

* toc
{:toc}

## インデックスAPI
公式ドキュメント：[Index APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices.html){:target="_blank"}

```
# インデックス一覧（cat indices API）
GET /_cat/indices

# インデックス取得（Get index API）
GET /pmacho-index

# インデックス存在確認（Exists API）
HEAD /pmacho-index

## 存在する場合
HTTP/1.1 200 OK
X-elastic-product: Elasticsearch
content-type: application/json
Transfer-Encoding: chunked

## 存在しない場合
HTTP/1.1 404 Not Found
X-elastic-product: Elasticsearch
content-type: application/json
content-length: 401

# インデックス作成（Create index API）
PUT /pmacho-index

# インデックス削除（Delete index API）
DELETE /pmacho-index
```

## ドキュメントAPI
公式ドキュメント：[Document APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs.html){:target="_blank"}

```
# ドキュメント取得（Get API）
GET /pmacho-index/_doc/1

# ドキュメント作成／更新（Index API）

## IDを指定しない
POST /pmacho-index/_doc
{"foo": 1, "bar": 2}

{
  "_index": "pmacho-index",
  "_id": "OVKaXY8BlRtbcrygOCAC",
  ...
}

## IDを指定する
PUT /pmacho-index/_doc/1
{"foo": 1, "bar": 2}

{
  "_index": "pmacho-index",
  "_id": "1",
  ...
}

# ドキュメント削除（Delete API）
DELETE /pmacho-index/_doc/1
```
