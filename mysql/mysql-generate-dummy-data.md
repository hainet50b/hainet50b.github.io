# MySQL 大量ダミーデータ作成

都合の良いツールが存在しておらず、地道にバルクインサートを行う以外に良い方法がない。  
大量レコードをINSERTするSQLファイルを外部で作成して取り込む方法もあるが、メモリ上限に抵触しないように注意する。

```sql
CREATE TABLE emp (
  id INT AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  PRIMARY KEY(id)
);

DELIMITER //

CREATE PROCEDURE InsertDummyUsers(num INT, blockSize INT)
BEGIN
  DECLARE i INT DEFAULT 0;
  DECLARE name VARCHAR(255);
  DECLARE records TEXT DEFAULT '';

  WHILE i < num DO
    SET name = CONCAT('user-', LPAD(FLOOR(RAND() * 999999), 6, '0'));

    -- バルクインサートを前提にする。
    SET records = CONCAT(
      records, 
      '(', QUOTE(name), '),'
    );

    IF i % blockSize = 0 THEN
      -- コミットのオーバーヘッドを最小限にする。
      START TRANSACTION;

      -- 末尾のカンマを削除する。
      SET records = LEFT(records, CHAR_LENGTH(records) - 1);

      SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', records);
      PREPARE stmt FROM @sql;
      EXECUTE stmt;
      DEALLOCATE PREPARE stmt;

      SET records = '';

      COMMIT;
    END IF;

    SET i = i + 1;
  END WHILE;

  IF LENGTH(records) > 0 THEN
    START TRANSACTION;

    SET records = LEFT(records, CHAR_LENGTH(records) - 1);

    SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', records);
    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;

    COMMIT;
  END IF;

END //

DELIMITER ;

SELECT
  routine_definition
FROM
  information_schema.routines
WHERE
  routine_name = 'InsertDummyUsers';

CALL InsertDummyUsers(1000, 100);
```

データ容量はinformation_schema.tablesで確認できる。

```sql
-- 統計情報を取得してからデータ容量を確認すること。
ANALYZE TABLE emp;

SELECT
  table_schema AS 'Database',
  table_name AS 'Table',
  table_rows AS 'Rows',
  ROUND(data_length / POW(1024, 3), 3) AS 'Data(GB)',
  ROUND(index_length / POW(1024, 3), 3) AS 'Index(GB)',
  ROUND((data_length + index_length) / POW(1024, 3), 3) AS 'Sum(GB)'
FROM
  information_schema.tables
WHERE
  table_schema = 'pmacho_db'
  AND table_name = 'emp';
```
