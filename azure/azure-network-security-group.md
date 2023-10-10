# Azure ネットワークセキュリティグループ
{:.no_toc}

* toc
{:toc}

## ネットワークセキュリティグループの作成
```shell
az network nsg create -n pmacho-nsg -g pmacho-rg -l eastus
```

## ネットワークセキュリティグループの確認
```shell
az network nsg list -o table

Location    Name        ProvisioningState    ResourceGroup    ResourceGuid
----------  ----------  -------------------  ---------------  ------------------------------------
eastus      pmacho-nsg  Succeeded            pmacho-rg        *******-****-****-****-*************
```

## セキュリティルールの追加
特定のIPアドレスからSSH, HTTP, HTTPSを許可する設定は以下の通り。

```shell
az network nsg rule create \
  -n inbound-allow-ssh-from-hainet50b \
  -g pmacho-rg \
  --nsg-name pmacho-nsg \
  --priority 100 \
  --direction inbound \
  --protocol tcp \
  --source-address-prefix 192.0.2.1 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22 \
  --access allow

az network nsg rule create \
  -n inbound-allow-http-from-hainet50b \
  -g pmacho-rg \
  --nsg-name pmacho-nsg \
  --priority 110 \
  --direction inbound \
  --protocol tcp \
  --source-address-prefix 192.0.2.1 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80 \
  --access allow

az network nsg rule create \
  -n inbound-allow-https-from-hainet50b \
  -g pmacho-rg \
  --nsg-name pmacho-nsg \
  --priority 120 \
  --direction inbound \
  --protocol tcp \
  --source-address-prefix 192.0.2.1 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 443 \
  --access allow
```

## セキュリティルールの確認
```shell
az network nsg rule list --nsg-name pmacho-nsg -g pmacho-rg -o table

Name                                ResourceGroup    Priority    SourcePortRanges    SourceAddressPrefixes    SourceASG    Access    Protocol    Direction    DestinationPortRanges    DestinationAddressPrefixes    DestinationASG
----------------------------------  ---------------  ----------  ------------------  -----------------------  -----------  --------  ----------  -----------  -----------------------  ----------------------------  ----------------
inbound-allow-ssh-from-hainet50b    pmacho-rg        100         *                   192.0.2.1                None         Allow     Tcp         Inbound      22                       *                             None
inbound-allow-http-from-hainet50b   pmacho-rg        110         *                   192.0.2.1                None         Allow     Tcp         Inbound      80                       *                             None
inbound-allow-https-from-hainet50b  pmacho-rg        120         *                   192.0.2.1                None         Allow     Tcp         Inbound      443                      *                             None
```

## セキュリティルールの削除
```shell
az network nsg rule delete -n inbound-allow-ssh-from-hainet50b -g pmacho-rg --nsg-name pmacho-nsg
```

## ネットワークセキュリティグループの削除
```shell
az network nsg delete -n pmacho-nsg -g pmacho-rg
```
