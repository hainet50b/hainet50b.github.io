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
  DECLARE users TEXT DEFAULT '';

  -- コミットのオーバーヘッドを最小限にする。
  START TRANSACTION;

  WHILE i < num DO
    SET name = CONCAT('user-', LPAD(FLOOR(RAND() * 999999), 6, '0'));

    -- バルクインサートを前提にする。
    SET users = CONCAT(
      users, 
      '(', QUOTE(name), '),'
    );

    IF i % blockSize = 0 THEN
      -- 末尾のカンマを削除する。
      SET users = LEFT(users, CHAR_LENGTH(users) - 1);

      SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', users);
      PREPARE stmt FROM @sql;
      EXECUTE stmt;
      DEALLOCATE PREPARE stmt;

      SET users = '';
    END IF;

    SET i = i + 1;
  END WHILE;

  IF LENGTH(users) > 0 THEN
    SET users = LEFT(users, CHAR_LENGTH(users) - 1);

    SET @sql = CONCAT ('INSERT INTO emp (name) VALUES ', users);
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