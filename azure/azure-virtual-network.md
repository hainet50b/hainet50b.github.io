# Azure 仮想ネットワーク
{:.no_toc}

* toc
{:toc}

## 仮想ネットワークの作成
```shell
az network vnet create \
  -n pmacho-vnet \
  -g pmacho-rg \
  -l eastus \
  --address-prefix 10.0.0.0/16
```

## 仮想ネットワークの確認
```shell
az network vnet list --query "[].[name, resourceGroup, location, addressSpace.addressPrefixes, provisioningState]" -o table

Column1      Column2    Column3    Column4          Column5
-----------  ---------  ---------  ---------------  ---------
pmacho-vnet  pmacho-rg  eastus     ['10.0.0.0/16']  Succeeded
```

## サブネットの作成
```shell
az network vnet subnet create \
  -n pmacho-subnet \
  -g pmacho-rg \
  --vnet-name pmacho-vnet \
  --address-prefix 10.0.0.0/24
```

## サブネットの確認
```shell
az network vnet subnet list --vnet-name pmacho-vnet -g pmacho-rg -o table

AddressPrefix    Name           PrivateEndpointNetworkPolicies    PrivateLinkServiceNetworkPolicies    ProvisioningState    ResourceGroup
---------------  -------------  --------------------------------  -----------------------------------  -------------------  ---------------
10.0.0.0/24      pmacho-subnet  Disabled                          Enabled                              Succeeded            pmacho-rg
```

## サブネットの削除
```shell
az network vnet subnet delete -n pmacho-subnet -g pmacho-rg --vnet-name pmacho-vnet
```

## 仮想ネットワークの削除
```shell
az network vnet delete -n pmacho-vnet -g pmacho-rg
```
