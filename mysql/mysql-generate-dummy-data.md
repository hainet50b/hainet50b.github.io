# MySQL 大量ダミーデータ作成

都合の良いツールが存在しておらず、地道にバルクインサートを行う以外に良い方法がない。

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

  -- コミットのオーバーヘッドを最小限にする。
  START TRANSACTION;

  WHILE i < num DO
    SET name = CONCAT('user-', LPAD(FLOOR(RAND() * 999999), 6, '0'));

    -- バルクインサートを前提にする。
    SET records = CONCAT(
      records, 
      '(', QUOTE(name), '),'
    );

    IF i % blockSize = 0 THEN
      -- 末尾のカンマを削除する。
      SET records = LEFT(records, CHAR_LENGTH(records) - 1);

      SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', records);
      PREPARE stmt FROM @sql;
      EXECUTE stmt;
      DEALLOCATE PREPARE stmt;

      SET records = '';
    END IF;

    SET i = i + 1;
  END WHILE;

  IF LENGTH(records) > 0 THEN
    SET records = LEFT(records, CHAR_LENGTH(records) - 1);

    SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', records);
    PREPARE stmt FROM @sql;
    EXECUTE stmt;
    DEALLOCATE PREPARE stmt;
  END IF;

  COMMIT;
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