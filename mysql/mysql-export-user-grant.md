# MySQL ユーザーと権限をCREATE TABLE/GRANT形式でエクスポート
{:.no_toc}

* toc
{:toc}

MySQLのmysql.user, mysql.dbテーブルはINSERT文による挿入を受け付けない。  
そのためユーザーと権限を復元する際にはCREATE TABLE/GRANT文を発行しなければならない。

## ユーザーのエクスポート
パスワードを確認できないため、別途差し替える必要がある。

```sql
SELECT
  User, Host,
  CONCAT('CREATE USER \'', User, '\'@\'', Host, '\' ', 'IDENTIFIED BY <password>;')
FROM
  mysql.user
WHERE
  User != 'root'
  AND User != ''
  AND User NOT LIKE 'mysql.%';
```

## 権限のエクスポート
一切の権限を持たないユーザーについては適切に動作しない。

```sql
SELECT
  Db, User, Host,
  CONCAT(
    SUBSTRING(grants, 1, LENGTH(grants) - 2),
    target
  ) AS Grant_statement
FROM (
  SELECT
    Db, User, Host,
    CONCAT(
      'GRANT ',
      IF(Select_priv = 'Y', 'SELECT, ', ''),
      IF(Insert_priv = 'Y', 'INSERT, ', ''),
      IF(Update_priv = 'Y', 'UPDATE, ', ''),
      IF(Delete_priv = 'Y', 'DELETE, ', ''),
      IF(Create_priv = 'Y', 'CREATE, ', ''),
      IF(Drop_priv = 'Y', 'DROP, ', ''),
      IF(Grant_priv = 'Y', 'GRANT OPTION, ', ''),
      IF(References_priv = 'Y', 'REFERENCES, ', ''),
      IF(Index_priv = 'Y', 'INDEX, ', ''),
      IF(Alter_priv = 'Y', 'ALTER, ', ''),
      IF(Create_tmp_table_priv = 'Y', 'CREATE TEMPORARY TABLES, ', ''),
      IF(Lock_tables_priv = 'Y', 'LOCK TABLES, ', ''),
      IF(Create_view_priv = 'Y', 'CREATE VIEW, ', ''),
      IF(Show_view_priv = 'Y', 'SHOW VIEW, ', ''),
      IF(Create_routine_priv = 'Y', 'CREATE ROUTINE, ', ''),
      IF(Alter_routine_priv = 'Y', 'ALTER ROUTINE, ', ''),
      IF(Execute_priv = 'Y', 'EXECUTE, ', ''),
      IF(Event_priv = 'Y', 'EVENT, ', ''),
      IF(Trigger_priv = 'Y', 'TRIGGER, ', '')
    ) AS grants,
    CONCAT(
      ' ON ', Db, '.* TO \'', User, '\'@\'', Host, '\';'
    ) AS target
  FROM
    mysql.db
  WHERE
    Db != 'sys'
    AND Db != 'mysql'
    AND Db != 'information_schema'
    AND Db != 'performance_schema'
) AS grants_target;
```
