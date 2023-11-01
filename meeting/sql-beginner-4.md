# SQL Beginner #4（データの挿入・更新・削除）
{:.no_toc}

* toc
{:toc}

## 二次元表の表現（VALUES句）
MySQLはサポートしていないが、二次元表をVALUES句で表現できることを知っておくと幅が広がる。

```sql
SELECT * FROM (VALUES (1, 'foo'));

SELECT * FROM (VALUES
  (1, 'foo'),
  (2, 'bar'),
  (3, 'baz'),
);

-- MySQLではVALUES句をサポートしていない。
SELECT 1, 'foo';

-- MySQLで複数行の二次元表を表現するにはUNIONを使用する。
SELECT 1, 'foo'
UNION SELECT 2, 'bar'
UNION SELECT 3, 'baz';
```

## データの挿入（INSERT）
サンプルのテーブルは以下の通り。
```sql
CREATE TABLE emp (
  id INT,
  name VARCHAR(32),
  age INT DEFAULT 0,
  PRIMARY KEY(id)
);
```

```sql
-- INSERT文の構文
-- リストは"(1, 2, 3)"のように"()"で囲んで取り扱う。
INSERT INTO ${テーブル名} ${列リスト} VALUES ${値リスト};

-- データの挿入
INSERT INTO emp (id, name, age) VALUES (1, 'foo', 20);
-- 複数行のデータの挿入
INSERT INTO emp (id, name, age) VALUES
  (11, 'foo', 21),
  (12, 'bar', 22),
  (13, 'baz', 23);

-- DEFAULT制約を設定した列を省略
INSERT INTO emp (id, name) VALUES (21, 'foo');
-- DEFAULTキーワードを明示的に指定
INSERT INTO emp (id, name, age) VALUES (22, 'bar', DEFAULT);
-- 制約の無い列を省略するとNULLとなる。
INSERT INTO emp (id) VALUES (23);
SELECT * FROM emp WHERE id = 23;
+----+------+------+
| id | name | age  |
+----+------+------+
| 23 | NULL |    0 |
+----+------+------+

-- NULLの挿入
-- NULLはDEFAULT制約に優先する。
INSERT INTO emp (id, name, age) VALUES (31, NULL, NULL);
SELECT * FROM emp WHERE id = 31;
+----+------+------+
| id | name | age  |
+----+------+------+
| 31 | NULL | NULL |
+----+------+------+
```

## 二次元表の挿入（SELECT INSERT）
INSERT文のVALUES句は二次元表であるため、SELECT文で差し替えられる。  
SELECT INSERTは特別な機能などではなく、ただ取得した二次元表を通常通りINSERTしているだけと考える。

```sql
CREATE TABLE emp2 (
  id INT,
  name VARCHAR(32),
  age INT DEFAULT 0,
  PRIMARY KEY(id)
);

SELECT COUNT(*) FROM emp; -- 8
SELECT COUNT(*) FROM emp2; -- 0

INSERT INTO emp2
SELECT * FROM emp;

SELECT COUNT(*) FROM emp; -- 8
SELECT COUNT(*) FROM emp2; -- 8

-- 二次元表の選択・射影が可能
INSERT INTO emp2 (id, name)
SELECT id, name FROM emp WHERE id < 20;

-- MySQLの二次元表表現を使って以下のようにVALUES句を差し替えられる。
INSERT INTO emp (id, name)
SELECT 1, 'foo'
UNION SELECT 2, 'bar'
UNION SELECT 3, 'baz';
```

## データの更新（UPDATE）
```sql
-- 単一の列を更新
-- すべての行が更新される。暗黙のWHERE 1が付与されていると解釈すると見通しが良い。
UPDATE emp SET age = 20;

-- 複数の列を更新
UPDATE emp SET
  name = 'foo',
  age = 30;

-- 特定の行を更新
-- UPDATE文を発行するときは必ずSELECT文で対象を確認すること。
SELECT * FROM emp WHERE id = 1; -- age = 0
UPDATE emp SET age = 20 WHERE id = 1;
SELECT * FROM emp WHERE id = 1; -- age = 20

-- SET句は値を受けるため値を返すSQLも記述できる。
UPDATE emp SET age = (SELECT 1);

-- 算術演算子を使用した式も記述できる。
UPDATE emp SET age = age + 1;
```

## データの削除（DELETE）
```sql
-- すべての行が削除される。暗黙のWHERE 1が付与されていると解釈すると見通しが良い。
DELETE FROM emp;

-- 特定のデータの削除
-- DELETE文を発行するときは必ずSELECT文で対象を確認すること。
SELECT * FROM emp WHERE id = 1; -- 1件
DELETE FROM emp WHERE id = 1;
SELECT * FROM emp WHERE id = 1; -- 0件
```

## すべてのデータの削除（TRUNCATE）
TRUNCATEはRDBMSによってDMLとして取り扱われたりDDLとして取り扱われたりする。  
危険を避けるため基本的にはDDLである（＝トランザクションが効かない）と考えて取り扱う方が良い。

```sql
TRUNCATE TABLE emp;
```
