# よく使うFly（Concourse CLI）コマンド

## ログイン
```shell
fly -t pmacho-dev login -c https://concourse.programacho.com -n pmacho

# 2回目以降はエンドポイントとチームの指定不要
fly -t pmacho-dev

# ターゲット一覧の確認
fly targets
TODO

# ターゲット一覧の編集
# ターゲットの情報は.flyrcファイルに記録される
cat ~/.flyrc

# TODO: 複数のチームにログインできるのか？
```

## パイプライン
```shell
# パイプラインの作成 -p パイプライン名 -c 設定ファイル
fly -t pmacho-dev sp -p pmacho-pipeline -c pipeline.yml

# TODO: credentials.yml

# パイプライン一覧
fly -t pmacho-dev pipelines

# TODO: パイプラインの削除
fly -t pmacho-dev dp -p pmacho-pipeline
```

## ジョブ
```shell
# ジョブを起動する
fly -t pmacho-dev tj -j pmacho-pipeline/unit-test --watch

# TODO: ジョブを停止する

# TODO: ジョブをpause/pause解除する
```

## コンテナ
```shell
TODO: fly -t pmacho-dev hijak -c ...
```
