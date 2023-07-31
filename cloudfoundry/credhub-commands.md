# よく使うCredHubコマンド

## ログイン
```shell
# ログイン
credhub login -s https://credhub.programacho.com -u hainet50b

# ログイン情報はconfig.jsonに記録される
cat ~/.credhub/config.json
```

## クレデンシャル
```shell
#  クレデンシャル一覧
credhub find

credentials:
- name: pmacho-key
  version_created_at: "2023-07-31T07:12:57Z"

# クレデンシャル設定
credhub set -n pmacho-key -t value -v pmacho-value

# クレデンシャル確認
credhub get -n pmacho-key

id: ec19a4fd-2978-4360-a37d-b423d03bc82c
name: pmacho-key
type: value
value: pmacho-value
version_created_at: "2023-07-31T07:12:57Z"

# クレデンシャル削除
credhub delete -n pmacho-key
```

## クレデンシャルの種類
```shell
# value
credhub set -n pmacho-key -t value -v pmacho-value
credhub get -n pmacho-key

id: ec19a4fd-2978-4360-a37d-b423d03bc82c
name: pmacho-key
type: value
value: pmacho-value
version_created_at: "2023-07-31T07:12:57Z"

# user
credhub set -n pmacho-user -t user --username pmacho-user --password changeme
credhub get -n pmacho-user

id: 426d2d64-7aeb-45fa-848b-907f170fe01f
name: pmacho-user
type: user
value:
  password: changeme
  password_hash: $6$Y/CM2jhu$gwyrTi04NVeFJrfalh2BGqZCGjySoyXYsjoeG23zADdEC4JMp.3w5sGjOSOrCSS4XLUXSjgcsxMA3LjU8ehiy1
  username: pmacho-user
version_created_at: "2023-07-31T07:17:38Z"
```
