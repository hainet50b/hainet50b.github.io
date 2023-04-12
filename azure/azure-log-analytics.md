# Azure Log Analytics

## Log Analyticsワークスペース
### ワークスペースを作成する
```shell
az monitor log-analytics workspace create \
  -n pmacho-laws \
  -g pmacho-rg \
  -l eastus \
  --retention-time 90
```

### 作成結果を確認する
```shell
az monitor log-analytics workspace show \
  -g pmacho-rg \
  -n pmacho-laws \
  -o table
```