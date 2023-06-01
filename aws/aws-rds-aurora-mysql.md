# AWS RDS Aurora MySQL

## データベースの作成
```shell
# クラスタの作成
aws rds create-db-cluster \
  --db-cluster-identifier pmacho-aurora-mysql \
  --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.03.1 \
  --master-username admin \
  --master-user-password changeme \
  --deletion-protection

# ライターインスタンスの作成
aws rds create-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-1 \
  --db-instance-class db.r6g.large \
  --db-cluster-identifier pmacho-aurora-mysql \
  --engine aurora-mysql

# リーダーインスタンスの作成（名前を変更して複数回実行可能）
aws rds create-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-2 \
  --db-instance-class db.r6g.large \
  --db-cluster-identifier pmacho-aurora-mysql \
  --engine aurora-mysql

# エンジンバージョンの取得
aws rds describe-db-engine-versions \
  --engine aurora-mysql \
  --query "DBEngineVersions[].EngineVersion"

# エンジンバージョンとインスタンスクラスの組み合わせの取得
aws rds describe-orderable-db-instance-options \
  --engine aurora-mysql \
  --engine-version 8.0 \
  --query "OrderableDBInstanceOptions[].[Engine,EngineVersion,StorageType,DBInstanceClass]" \
  --region ap-northeast-1
```

## データベースの確認
```shell
# データベースクラスタ一覧
aws rds describe-db-clusters \
  --query "DBClusters[].[DBClusterIdentifier]" \
  --output table

# データベースクラスタ詳細
aws rds describe-db-clusters \
  --db-cluster-identifier pmacho-aurora-mysql \
  --output table

# データベースインスタンス一覧
aws rds describe-db-clusters \
  --db-cluster-identifier pmacho-aurora-mysql \
  --query "DBClusters[].DBClusterMembers[].[DBInstanceIdentifier]" \
  --output table
```

## データベースクラスタの削除保護
```shell
# 削除保護を付与
aws rds modify-db-cluster \
  --db-cluster-identifier pmacho-aurora-mysql \
  --deletion-protection

# 削除保護を解除
aws rds modify-db-cluster \
  --db-cluster-identifier pmacho-aurora-mysql \
  --no-deletion-protection
```

削除保護されているクラスタの削除を試みると以下のメッセージが出力される。
```
An error occurred (InvalidParameterCombination) when calling the DeleteDBCluster operation:
Cannot delete protected Cluster, please disable deletion protection and try again.
```

## データベースの削除
```shell
# クラスタの削除
# 配下にインスタンス抱えているときはクラスタを削除できない。
# また、実運用ではスナップショットを取得してから削除することが望ましい。
aws rds delete-db-cluster \
  --db-cluster-identifier pmacho-aurora-mysql \
  --skip-final-snapshot

# インスタンスの削除
aws rds delete-db-instance --db-instance-identifier pmacho-aurora-mysql-instance-1
```
