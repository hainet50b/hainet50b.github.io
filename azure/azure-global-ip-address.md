# Azure グローバルIPアドレス
{:.no_toc}

* toc
{:toc}

## グローバルIPアドレスの発行
```shell
az network public-ip create \
  -n pmacho-global-ip \
  -g pmacho-rg \
  -l eastus \
  # SKUをStandardにすると可用性ゾーンやロードバランサーに対応できる。
  --sku Basic \
  --allocation-method Static
```

## グローバルIPアドレスの確認
```shell
az network public-ip list -o table

Name              ResourceGroup    Location    Zones    Address      IdleTimeoutInMinutes    ProvisioningState
----------------  ---------------  ----------  -------  -----------  ----------------------  -------------------
pmacho-global-ip  pmacho-rg        eastus               192.168.2.1  4                       Succeeded
```

## グローバルIPアドレスの削除
```shell
az network public-ip delete -n pmacho-global-ip -g pmacho-rg
```
