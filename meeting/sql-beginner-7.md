# SQL Beginner #7（集合演算・結合）

## 事前準備
```sql
CREATE TABLE foo (
  id INT,
  name VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO foo VALUES
  (1, 'foo'),
  (2, 'bar');

CREATE TABLE bar (
  id INT,
  name VARCHAR(32),
  PRIMARY KEY (id)
);

INSERT INTO bar VALUES
  (2, 'bar'),
  (3, 'baz');
```

## レコードの集合演算
```sql
-- 和
SELECT * FROM foo;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
+----+------+

SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  2 | bar  |
|  3 | baz  |
+----+------+

-- 重複行（3, baz）は取り除かれる。
SELECT * FROM foo
UNION
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
|  3 | baz  |
+----+------+

-- ALLオプションを付与すると重複行を取り除かない。
SELECT * FROM foo
UNION ALL
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
|  2 | bar  |
|  2 | bar  |
|  3 | baz  |
+----+------+

-- 列が一致している必要がある。
SELECT id, name, 1 FROM foo
UNION
SELECT * FROM bar;
ERROR 1222 (21000): The used SELECT statements have a different number of columns

-- ORDER BY句は集合演算結果となる二次元表にのみ適用できる。
SELECT * FROM foo
UNION
SELECT * FROM bar
ORDER BY id DESC;
+----+------+
| id | name |
+----+------+
|  3 | baz  |
|  2 | bar  |
|  1 | foo  |
+----+------+

-- 積
SELECT * FROM foo
INTERSECT
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  2 | bar  |
+----+------+

-- 差
SELECT * FROM foo
EXCEPT
SELECT * FROM bar;
+----+------+
| id | name |
+----+------+
|  1 | foo  |
+----+------+

-- 差は和や積と異なり記述順序によって結果が変わる。
SELECT * FROM bar
EXCEPT
SELECT * FROM foo;
+----+------+
| id | name |
+----+------+
|  3 | baz  |
+----+------+
```
