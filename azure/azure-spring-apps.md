# Azure Spring Apps
{:.no_toc}

* toc
{:toc}

## Azure CLI Azure Spring Apps拡張
### 拡張を導入する
```shell
az extension add -n spring
```

### 導入結果を確認する
```shell
az extension list -o table
```
```
Experimental    ExtensionType    Name    Path                                          Preview    Version
--------------  ---------------  ------  --------------------------------------------  ---------  ---------
False           whl              spring  /Users/hainet50b/.azure/cliextensions/spring  False      1.9.1
```

## Azure CLI デフォルト設定
リソースグループとAzure Spring Appsインスタンスを省略することができる。  
構築時よりも運用時に便利に活用することができる。
```shell
az configure -l -d group=pmacho-rg spring=pmacho-asa
```

## Azure Spring Apps サービスインスタンス
### サービスインスタンスを作成する
```shell
az spring create \
  -n pmacho-asa \
  -g pmacho-rg \
  -l eastus \
  --sku Standard \
  --zone-redundant true \
  --disable-app-insights true
```

### 作成結果を確認する
```shell
az spring list -o table
```

## Azure Spring Apps アプリケーション空間
### アプリケーション空間を作成
```shell
az spring app create \
  -n payment-gateway \
  -g pmacho-rg \
  -s pmacho-asa \
  --cpu 500m \
  --memory 512Mi \
  --runtime-version Java_17 \
  --instance-count 1 \
  --assign-endpoint true
```

### 作成結果を確認する
```shell
az spring app list \
  -g pmacho-rg \
  -s pmacho-asa \
  -o table
```

## Log Analytics設定
### Log Analyticsの設定をする
[Azure Log Analytics | pages.programacho.com](https://pages.programacho.com/azure/azure-log-analytics){:target="_blank"}

### サービスインスタンスに診断設定を作成する
```shell
az monitor diagnostic-settings create \
  -n pmacho-asa-ds \
  -g pmacho-rg \
  --resource pmacho-asa \
  --resource-type Microsoft.AppPlatform/Spring \
  --workspace pmacho-laws \
  --logs @pmacho-asa-ds-logs.json
```
```json:pmacho-asa-ds-logs.json
{
  "category": "ApplicationConsole",
  "enabled": true,
  "retentionPolicy": {
    "enabled": false,
    "days": 0
  }
}
```

## Application Insights設定
### Application Insightsの設定をする
[Azure Application Insights | pages.programacho.com](https://pages.programacho.com/azure/azure-application-insights){:target="_blank"}

### サービスインスタンスにApplication Insightsを紐付ける
```shell
az spring app-insights update \
  -g pmacho-rg \
  -n pmacho-asa \
  --app-insights pmacho-ai \
  --sampling-rate 100
```

## アプリケーションデプロイ
### アプリケーションをデプロイする
サンプルアプリケーションをデプロイする場合はこのコマンドで良い。  
実用上はブルーグリーンデプロイを達成するために後述するデプロイメントを指定したコマンドを使用する。
```shell
az spring app deploy \
  -n payment-gateway \
  -g pmacho-rg \
  -s pmacho-asa \
  --artifact-path target/payment-gateway.jar \
  --env spring.profiles.active=cloud
```

### デプロイしたアプリケーションを確認する
```shell
az spring app show \
  -n payment-gateway \
  -g pmacho-rg \
  -s pmacho-asa
```

## Azure Spring Apps ログ設定
### ログを確認する
```shell
az spring app logs -f \
  -n payment-gateway \
  -g pmacho-rg \
  -s pmacho-asa
```

## ブルーグリーンデプロイ
アプリケーションをデプロイするたびに新たに名前付きデプロイメントを作成する。  
以下でバージョン名を指定することが紹介されているが、運用上の同一バージョンリリースができなくなるため日時を採用する。  
[Azure Spring Apps におけるブルーグリーン デプロイ戦略#名前付きデプロイ](https://learn.microsoft.com/ja-jp/azure/spring-apps/concepts-blue-green-deployment-strategies#named-deployments){:target="_blank"}

### アプリケーションをSTG環境にデプロイする
```shell
az spring app deployment create \
  -n default-$(date "+%Y%m%d%H%M%S") \
  -g pmacho-rg \
  -s pmacho-asa \
  -app payment-gateway \
  --artifact-path target/payment-gateway.jar \
  --env spring.profiles.active=cloud
```

### STG環境のデプロイメント名を取得する
```shell
deployment=$(az spring app deployment list \
  -g pmacho-rg \
  -s pmacho-asa \
  --app payment-gateway \
  --query "[?properties.active == \`false\`].name" \
  -o tsv \
)
```

### STG環境を本番環境とスイッチする
```shell
az spring app set-deployment \
  -d $deployment \
  -g pmacho-rg \
  -s pmacho-asa \
  -n payment-gateway
```
