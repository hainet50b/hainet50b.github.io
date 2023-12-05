# Oracle セッション管理
{:.no_toc}

* toc
{:toc}

## データベースごとのセッションを確認する
```sql
SELECT
  inst_id      AS 接続先,
  machine      AS ホスト名,
  service_name AS サービス名,
  username     AS スキーマ名,
  COUNT(*)     AS 接続数
FROM gv$session
WHERE
  username != 'SYS'
  AND username IS NOT NULL
GROUP BY
  inst_id,
  machine,
  username,
  service_name
ORDER BY inst_id;
```

## 特定のデータベースのセッションを強制終了させる
```sql
SELECT DISTINCT
  sid,
  serial#,
  inst_id,
  service_name,
  username,
  machine,
  'ALTER SYSTEM KILL SESSION ''' || sid || ', ' || serial# || ', @' || inst_id || ''';' AS "SESSION KILL SQL"
FROM gv$session
WHERE service_name = 'pmacho'
ORDER BY
  inst_id,
  service_name,
  username,
  machine,
  sid,
  serial#;

SID,SERIAL#,INST_ID,SERVICE_NAME,USERNAME,MACHINE,SESSION KILL SQL
123,12345,1,pmacho,pmacho_user,hainet50b,"ALTER SYSTEM KILL SESSION '123, 12345, @1';"
```

## セッションで発行されたSQLを特定する
TODO
