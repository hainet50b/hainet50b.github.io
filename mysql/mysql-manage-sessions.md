# MySQL セッション管理
{:.no_toc}

* toc
{:toc}

## データベースごとのセッションを確認する
```sql
SELECT
  db       AS データベース,
  user     AS ユーザー,
  COUNT(*) AS 接続数
FROM information_schema.processlist
WHERE
  db IS NOT NULL
  AND db = 'programacho_db'
GROUP BY
  db,
  user
ORDER BY
  db,
  user;

+--------------------+--------------+-----------+
| データベース       | ユーザー     | 接続数    |
+--------------------+--------------+-----------+
| programacho_db     | root         |         1 |
+--------------------+--------------+-----------+
```

## 特定のデータベースのセッションを強制終了させる
```sql
SELECT
  id                       AS ID,
  db                       AS データベース,
  user                     AS ユーザー,
  CONCAT('KILL ', id, ';') AS セッションキルコマンド
FROM information_schema.processlist
WHERE
  db IS NOT NULL
  AND db = 'programacho_db'
ORDER BY
  db,
  user,
  id;

+----+--------------------+--------------+-----------------------------------+
| ID | データベース       | ユーザー     | セッションキルコマンド            |
+----+--------------------+--------------+-----------------------------------+
|  8 | programacho_db     | root         | KILL 8;                           |
+----+--------------------+--------------+-----------------------------------+
```
