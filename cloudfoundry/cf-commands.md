# よく使うcfコマンド

## ログイン
```
cf login [-a https://api.programacho.com] [-u programacho] [-p password] [-o org] [-s space]

# ログインしているAPIエンドポイント、組織、スペースを確認できる
cf target
API endpoint:   https://api.programacho.com
API version:    3.99.0
user:           20400616+hainet50b@users.noreply.github.com
org:            programacho
space:          develop
```

## デプロイ
```
# アプリケーションをデプロイ
cf push -p pmacho-app.jar -f manifest.yml

# アプリケーションを削除
cf delete pmacho-app
```

## アプリケーション
```
# アプリケーション一覧を確認
cf apps

# アプリケーションの詳細を確認
cf app pmacho-app

# アプリケーションを起動
cf start pmacho-app

# アプリケーションを再起動
cf restart pmacho-app

# アプリケーションを停止
cf stop pmacho-app
```

## ログ
```
# ログを確認
cf logs pmacho-app
```