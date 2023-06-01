# AWS RDS Aurora MySQL
{:.no_toc}

* toc
{:toc}

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
  --engine-version 8.0.mysql_aurora.3.03.1 \
  --query "OrderableDBInstanceOptions[].[Engine,EngineVersion,StorageType,DBInstanceClass]" \
  --region ap-northeast-1 \
  --output table
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
# 削除保護設定を確認

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

## Performance Insightsの有効化
```shell
# Performance Insights設定を確認
aws rds describe-db-instances \
  --query "DBInstances[].[DBInstanceIdentifier,PerformanceInsightsEnabled,PerformanceInsightsRetentionPeriod]" \
  --output table

# Performance Insightsを有効化
aws rds modify-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-1 \
  --enable-performance-insights \
  --performance-insights-retention-period 7

# Performance Insightsを無効化
aws rds modify-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-1 \
  --no-enable-performance-insights
```

## パブリックアクセスの有効化
```shell
# パブリックアクセス設定を確認
aws rds describe-db-instances \
  --query "DBInstances[].[DBInstanceIdentifier,PubliclyAccessible]" \
  --output table

# パブリックアクセス設定を有効化
aws rds modify-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-1 \
  --publicly-accessible

# パブリックアクセス設定を無効化
aws rds modify-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-instance-1 \
  --no-publicly-accessible
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

## スナップショットの取得
```shell
aws rds create-db-cluster-snapshot \
  --db-cluster-snapshot-identifier pmacho-aurora-mysql-snapshot \
  --db-cluster-identifier pmacho-aurora-mysql
```

## スナップショットの確認
```shell
aws rds describe-db-cluster-snapshots \
  --query "DBClusterSnapshots[].[DBClusterIdentifier,DBClusterSnapshotIdentifier,SnapshotCreateTime]" \
  --output table
```

## スナップショットの削除
```shell
aws rds delete-db-cluster-snapshot \
  --db-cluster-snapshot-identifier pmacho-aurora-mysql-snapshot
```

## スナップショットの復元
```shell
aws rds restore-db-cluster-from-snapshot \
  --db-cluster-identifier pmacho-aurora-mysql-backup \
  --snapshot-identifier pmacho-aurora-mysql-snapshot \
  --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.03.1 \
  --deletion-protection

aws rds create-db-instance \
  --db-instance-identifier pmacho-aurora-mysql-backup-instance-1 \
  --db-instance-class db.r6g.large \
  --db-cluster-identifier pmacho-aurora-mysql-backup \
  --engine aurora-mysql

# 新旧クラスタの差分確認
aws rds describe-db-clusters --db-cluster-identifier pmacho-aurora-mysql > running-cluster.json
aws rds describe-db-clusters --db-cluster-identifier pmacho-aurora-mysql-backup > snapshot-cluster.json
diff running-cluster.json snapshot-cluster.json

# 新旧インスタンスの差分確認
aws rds describe-db-instances --db-instance-identifier pmacho-aurora-mysql-instance-1 > running-instance.json
aws rds describe-db-instances --db-instance-identifier pmacho-aurora-mysql-backup-instance-1 > snapshot-instance.json
diff running-instance.json snapshot-instance.json

# パブリックアクセスやPerformance Insightsの再設定が必要となる。
< "PubliclyAccessible": true,
---
> "PubliclyAccessible": false,
...
< "PerformanceInsightsEnabled": true,
< "PerformanceInsightsKMSKeyId": "***",
< "PerformanceInsightsRetentionPeriod": 7,
---
> "PerformanceInsightsEnabled": false,
```

## Aurora MySQLのアップデート
```shell
# 2.07.8 -> 2.11.2
aws rds modify-db-cluster \
  --db-cluster-identifier pmacho-aurora-mysql \
  --engine-version 5.7.mysql_aurora.2.11.2 \
  --apply-immediately

# クラスタのバージョン確認
aws rds describe-db-clusters \
  --query "DBClusters[].[DBClusterIdentifier,EngineVersion]" \
  --output table
```