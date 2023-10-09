# Azure 仮想マシン

## 仮想マシンの作成
```shell
# 事前にSSH鍵を作成する。
ssh-keygen -t rsa -b 4096 -C '20400616+hainet50b@users.noreply.github.com' -f ~/.ssh/pmacho-vm

az vm create \
  -n pmacho-vm \
  -g pmacho-rg \
  -l eastus \
  --image Ubuntu2204 \
  --size Standard_B1s \
  --authentication-type ssh \
  --ssh-key-values ~/.ssh/pmacho-vm.pub \
  --admin-username hainet50b \
  --storage-sku StandardSSD_LRS \
  --os-disk-size-gb 30 \
  --os-disk-delete-option Delete \
  --vnet-name pmacho-vnet \
  --subnet pmacho-subnet \
  --nsg pmacho-nsg \
  --public-ip-address pmacho-global-ip
```

指定したグローバルIP宛てにSSH接続できることを確認する。
```shell
ssh 192.0.2.1 -l hainet50b -i ~/.ssh/pmacho-vm
```

### イメージ一覧の確認
```shell
az vm image list --output table
```

### サイズ一覧の確認
```shell
az vm list-sizes -l eastus -o table
```

## 仮想マシンの確認
```shell
az vm list -o table

Name       ResourceGroup    Location    Zones
---------  ---------------  ----------  -------
pmacho-vm  PMACHO-RG        eastus
```

## 仮想マシンの削除
```shell
az vm delete -n pmacho-vm -g pmacho-rg
```
