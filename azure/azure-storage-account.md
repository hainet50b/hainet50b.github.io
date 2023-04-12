# Azure Storage Account
{:.no_toc}

* toc
{:toc}

## ストレージアカウントの設定
### ストレージアカウントを作成する
```shell
az storage account create \
  -n programacho \
  -g pmacho-rg \
  -l eastus \
  --sku Standard_RAGRS \
  --access-tier Hot \
  --allow-blob-public-access true \
  --https-only true \
  --min-tls-version TLS1_2 \
  --public-network-access Enabled
```

### 作成結果を確認する
```shell
az storage account show \
  -n programacho \
  -o table
```
```
AccessTier    AllowBlobPublicAccess    CreationTime                      EnableHttpsTrafficOnly    Kind       Location    MinimumTlsVersion    Name         PrimaryLocation    ProvisioningState    PublicNetworkAccess    ResourceGroup    SecondaryLocation    StatusOfPrimary    StatusOfSecondary
------------  -----------------------  --------------------------------  ------------------------  ---------  ----------  -------------------  -----------  -----------------  -------------------  ---------------------  ---------------  -------------------  -----------------  -------------------
Hot           True                     2023-04-09T00:20:33.135852+00:00  True                      StorageV2  eastus      TLS1_2               programacho  eastus             Succeeded            Enabled                pmacho-rg        westus               available          available
```

### ストレージアカウントを削除する
```shell
az storage account delete \
  -n programacho \
  -g pmacho-rg
```

## コンテナの設定
### コンテナを作成する
```shell
az storage container create \
  -n images \
  --account-name programacho \
  --public-access blob
```

### 作成結果を確認する
```shell
az storage container show \
  -n images \
  --account-name programacho \
  -o table
```
```
Name    Lease Status    Last Modified
------  --------------  -------------------------
images  unlocked        2023-04-09T00:22:51+00:00
```

### コンテナを削除する
コンテナを削除すると配下のリソースにアクセスできなくなる。
```shell
az storage container delete \
  -n images \
  --account-name programacho
```

## BLOBストレージ
### ファイルをアップロードする
Azure Portalからアップロードする。
![azure-storage-account-798a8118d39f.png](https://programacho.blob.core.windows.net/images/azure-storage-account-798a8118d39f.png)

アップロードしたファイルのURLは以下のようになる。
```
https://programacho.blob.core.windows.net/images/azure-storage-account-798a8118d39f.png
```