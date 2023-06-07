# AWS EC2
{:.no_toc}

* toc
{:toc}

## インスタンスの起動（＝作成）
```shell
aws ec2 run-instances \
  --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=pmacho-ec2}]" \
  --image-id ami-*** \
  --instance-type t2.micro \
  --count 1 \
  --key-name *** \
  --security-group-ids sg-*** \
  --block-device-mappings file://mappings.json

# AMI一覧の取得
aws ec2 describe-images \
  --owners amazon \
  --filter \
    "Name=name,Values=al2023-ami-2023.*" \
    "Name=state,Values=available" \
    "Name=architecture,Values=x86_64" \
  --query "Images[].[Name,ImageId,CreationDate]" \
  --output text \
  | sort -k3 -r

# キーペア一覧の取得
aws ec2 describe-key-pairs --query "KeyPairs[].[KeyName]" --output text

# セキュリティグループ一覧の取得
aws ec2 describe-security-groups --query "SecurityGroups[].[GroupName,GroupId]" --output text

# mappings.json
[
  {
    "DeviceName": "/dev/xvda",
    "Ebs": {
      "VolumeSize": 10,
      "DeleteOnTermination": true,
      "VolumeType": "gp3"
    }
  }
]
```

## インスタンス一覧の確認
```shell
aws ec2 describe-instances \
  --query 'Reservations[].Instances[].[Tags[?Key==`Name`].Value | [0],InstanceId,State.Name]' \
  --output text
```

## インスタンスの開始・停止
```shell
# インスタンスの開始
aws ec2 start-instances --instance-ids i-***

# インスタンスの停止
aws ec2 stop-instances --instance-ids i-***
```

## インスタンスの削除
```shell
aws ec2 terminate-instances --instance-ids i-***
```
