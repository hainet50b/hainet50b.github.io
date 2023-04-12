# Azure Application Insights

## Azure CLI Application Insights拡張
### 拡張を導入する
```shell
az extension add -n application-insights
```

### 導入結果を確認する
```shell
az extentions list -o table
```
```
Experimental    ExtensionType    Name                  Path                                                        Preview    Version
--------------  ---------------  --------------------  ----------------------------------------------------------  ---------  ---------
False           whl              application-insights  /Users/hainet50b/.azure/cliextensions/application-insights  True       0.1.19
```

## Application Insightsコンポーネント
### コンポーネントを作成する
```shell
az monitor app-insights component create \
  -a pmacho-ai \
  -g pmacho-rg \
  -l eastus \
  --retention-time 90
```

### 作成したコンポーネントを確認する
```shell
az monitor app-insights component show \
  -a pmacho-ai \
  -g pmacho-rg
```
TODO: 出力形式をテーブル形式に変更する方が望ましいか確認する。
