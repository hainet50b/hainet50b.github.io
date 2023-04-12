# Azure

## ログイン
### ログインする
`az login`コマンドを実行するとブラウザが立ち上がってログインを求められる。
```shell
az login
```

## サブスクリプションの設定
### サプスクリプションを作成する
サブスクリプションの作成はコマンドラインではなくAzure Portalで行う。
![azure-d7c30117a454.png](https://programacho.blob.core.windows.net/images/azure-d7c30117a454.png)

### 作成結果を確認する
```shell
az account show -o table
```
```
EnvironmentName    HomeTenantId                          IsDefault    Name        State    TenantId
-----------------  ------------------------------------  -----------  ----------  -------  ------------------------------------
AzureCloud         ********-****-****-****-************  True         pmacho-sub  Enabled  ********-****-****-****-************
```

## リソースグループの設定
### リソースグループを作成する
```shell
az group create \
  -n pmacho-rg \
  -l eastus
```

### 作成結果を確認する
```shell
az group show \
  -n pmacho-rg \
  -o table
```
```
Location    Name
----------  ---------
eastus      pmacho-rg
```