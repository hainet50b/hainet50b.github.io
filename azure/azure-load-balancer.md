# Azure ロードバランサー
{:.no_toc}

* toc
{:toc}

## ロードバランサー

### ロードバランサーの作成
```shell
az network lb create \
  -n pmacho-lb \
  -g pmacho-rg \
  -l eastus \
  --sku Standard \
  --frontend-ip-name pmacho-lb-frontend-ip \
  --public-ip-address pmacho-lb-global-ip
```

### ロードバランサーの確認
```shell
az network lb list -o table

Location    Name       ProvisioningState    ResourceGroup    ResourceGuid
----------  ---------  -------------------  ---------------  ------------------------------------
eastus      pmacho-lb  Succeeded            pmacho-rg        ********-****-****-****-************
```

### ロードバランサーの削除
```shell
az network lb delete -n pmacho-lb -g pmacho-rg
```

## バックエンドプール

### バックエンドプールの作成
```shell
az network lb address-pool create \
  -n pmacho-lb-backend-pool \
  -g pmacho-rg \
  --lb-name pmacho-lb
```

### バックエンドプールの確認
```shell
az network lb address-pool list -g pmacho-rg --lb-name pmacho-lb -o table

Name                    ProvisioningState    ResourceGroup
----------------------  -------------------  ---------------
pmacho-lb-backend-pool  Succeeded            pmacho-rg
```

### バックエンドプールの削除
```shell
az network lb address-pool delete -n pmacho-lb-backend-pool -g pmacho-rg --lb-name pmacho-lb
```

### 仮想マシンをバックエンドプールに関連付ける
```shell
# 仮想マシンのNIC名を取得する。
NIC_NAME=$(basename $(az vm show \
  -n pmacho-vm \
  -g pmacho-rg \
  --query 'networkProfile.networkInterfaces[0].id' \
  -o tsv \
))

# IP設定名を取得する。
IP_CONFIG_NAME=$(az network nic ip-config list \
  -g pmacho-rg \
  --nic-name $NIC_NAME \
  --query '[0].name' \
  --output tsv \
)

# バックエンドプールにNICを紐付ける。
az network nic ip-config address-pool add \
  -n pmacho-vm-ipconfig \
  -g pmacho-rg \
  --lb-name pmacho-lb \
  --address-pool pmacho-lb-backend-pool \
  --nic-name $NIC_NAME \
  --ip-config-name $IP_CONFIG_NAME
```

### バックエンドプールに関連付けられたNICの確認
```shell
az network lb address-pool show \
  --name pmacho-lb-backend-pool \
  --resource-group pmacho-rg \
  --lb-name pmacho-lb \
  --query "backendIPConfigurations[].id" \
  -o table
```

### 仮想マシンをバックエンドプールの関連付けから解除する
```shell
# 仮想マシンのNIC名を取得する。
NIC_NAME=$(basename $(az vm show \
  -n pmacho-vm \
  -g pmacho-rg \
  --query 'networkProfile.networkInterfaces[0].id' \
  -o tsv \
))

# IP設定名を取得する。
IP_CONFIG_NAME=$(az network nic ip-config list \
  -g pmacho-rg \
  --nic-name $NIC_NAME \
  --query '[0].name' \
  --output tsv \
)

az network nic ip-config address-pool remove \
  --nic-name $NIC_NAME \
  --ip-config-name $IP_CONFIG_NAME \
  -g pmacho-rg \
  --lb-name pmacho-lb \
  --address-pool pmacho-lb-backend-pool
```

## ヘルスチェック設定

### ヘルスチェック設定の作成
以下の設定はTCPでヘルスチェックを実施する。

```shell
az network lb probe create \
  -n pmacho-lb-health-check \
  -g pmacho-rg \
  --lb-name pmacho-lb \
  --protocol tcp \
  --port 80 \
  --interval 5
```

### ヘルスチェック設定の確認
```shell
az network lb probe list -g pmacho-rg --lb-name pmacho-lb -o table

IntervalInSeconds    Name                    NumberOfProbes    Port    ProbeThreshold    Protocol    ProvisioningState    ResourceGroup
-------------------  ----------------------  ----------------  ------  ----------------  ----------  -------------------  ---------------
5                    pmacho-lb-health-check  2                 80      1                 Tcp         Succeeded            pmacho-rg
```

### ヘルスチェック設定の削除
```shell
az network lb probe delete -n pmacho-lb-health-check -g pmacho-rg --lb-name pmacho-lb
```

## 負荷分散設定

### 負荷分散設定の作成
```shell
az network lb rule create \
  -n pmacho-lb-rule \
  -g pmacho-rg \
  --lb-name pmacho-lb \
  --frontend-ip-name pmacho-lb-frontend-ip \
  --backend-pool-name pmacho-lb-backend-pool \
  --protocol tcp \
  --frontend-port 80 \
  --backend-port 80 \
  --probe-name pmacho-lb-health-check
```

### 負荷分散設定の確認
```shell
az network lb rule list -g pmacho-rg --lb-name pmacho-lb -o table

BackendPort    DisableOutboundSnat    EnableFloatingIP    EnableTcpReset    FrontendPort    IdleTimeoutInMinutes    LoadDistribution    Name            Protocol    ProvisioningState    ResourceGroup
-------------  ---------------------  ------------------  ----------------  --------------  ----------------------  ------------------  --------------  ----------  -------------------  ---------------
80             False                  False               False             80              4                       Default             pmacho-lb-rule  Tcp         Succeeded            pmacho-rg
```

### 負荷分散設定の削除
```shell
az network lb rule delete -n pmacho-lb-rule -g pmacho-rg --lb-name pmacho-lb
```
