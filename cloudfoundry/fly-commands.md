# よく使うFly（Concourse CLI）コマンド

## ログイン
```shell
fly -t pmacho-dev login -c https://concourse.dev.programacho.com -n pmacho

# 2回目以降はエンドポイントとチームの指定は不要
fly -t pmacho-dev

# ターゲット一覧の確認
fly targets
name        url                                    team    expiry
pmacho-dev  https://concourse.dev.programacho.com  pmacho  Sat, 29 Jul 2023 13:48:20 UTC

# ターゲット一覧の編集
# ターゲットの情報は.flyrcファイルに記録される
# トークンが有効であれば複数のターゲットへのログイン状態を維持できる
cat ~/.flyrc
targets:
  pmacho-dev:
    api: https://concourse.dev.programacho.com
    team: pmacho
    token:
      type: bearer
      value: **********/***************************
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
# ジョブを起動する -w (--watch) 標準出力を閲覧する
fly -t pmacho-dev tj -j pmacho-pipeline/unit-test -w

# ジョブをpause/unpauseする
# pause
fly -t pmacho-dev pj -j pmacho-pipeline/unit-test

# unpause
fly -t pmacho-dev uj -j pmacho-pipeline/unit-test
```
