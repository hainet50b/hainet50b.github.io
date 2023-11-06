# SQL Beginner #6（述語・CASE式）

## 述語
SQLにおける述語とは真偽値を返却する関数をいう。

### 文字列の部分一致（LIKE）
```sql
-- 完全一致
SELECT 'foo' = 'foo'; -- 1
-- 前方一致
SELECT 'foo' LIKE 'fo%'; -- 1
-- 中間一致
SELECT 'foo' LIKE '%o%'; -- 1
-- 後方一致
SELECT 'foo' LIKE '%oo'; -- 1

-- 一文字一致（アンダーバー）
SELECT 'foo' LIKE 'f_o'; -- 1
SELECT 'foo' LIKE 'f__o'; -- 0
```

### 数値の範囲検索（BETWEEN）
```sql
SELECT 1 BETWEEN 0 AND 2; -- 1

-- 両端の値を含む
SELECT 1 BETWEEN 0 AND 1; -- 1
SELECT 1 BETWEEN 1 AND 2; -- 1

-- 文字列にも適用できるものの、なるべく使用しない。
SELECT 'b' BETWEEN 'a' AND 'c'; -- 1
SELECT '11' BETWEEN '1' AND '21'; -- 1
```

### 否定（NOT）
```SQL
SELECT NOT 1; -- 0
SELECT NOT 0; -- 1

-- 述語と組み合わせることができる。
SELECT NOT 1 BETWEEN 0 AND 2; -- 0
SELECT 1 NOT IN (1, 2, 3); -- 0
SELECT NOT EXISTS (SELECT 1); -- 0
```

### NULL判定（IS NULL / IS NOT NULL）
NOTでIS NULLを否定するだけでなく、特別にIS NOT NULL述語が用意されている。

```sql
SELECT 1 IS NULL; -- 0
SELECT NULL IS NULL; -- 1

SELECT 1 IS NOT NULL; -- 1
SELECT NULL IS NOT NULL; -- 0
```

### 値の集合内での存在判定（IN）
```sql
SELECT 1 IN (1, 2, 3); -- 1
SELECT 1 IN (2, 3); -- 0

-- n行1列（＝リスト）の二次元表にも適用できる。
SELECT 1 IN (
  SELECT 1
  UNION SELECT 2
  UNION SELECT 3
);
```

### レコードの存在判定（EXISTS）
```sql
-- 1行のレコードが存在するので真
SELECT EXISTS (
  SELECT 1, 'foo'
); -- 1

-- 3行のレコードが存在するので真
SELECT EXISTS (
  SELECT 1, 'foo'
  UNION SELECT 2, 'bar'
  UNION SELECT 3, 'baz'
); -- 1

-- 0行のレコードが存在するので偽
SELECT EXISTS (
  SELECT 1 WHERE 0
); -- 0
```

## 検索CASE式
最近のプログラミング言語によくある「値を返却するif文」とよく似ている。

```sql
SELECT
  CASE
    WHEN 1 THEN 'WHEN'
    ELSE 'ELSE'
  END; -- ELSE

-- 複数の条件に合致する場合は上から順に評価される。
SELECT
  CASE
    WHEN 0 THEN '1行目'
    WHEN 1 THEN '2行目'
    ELSE '3行目'
  END; -- 2行目

-- 複数の型を取り得るので注意して使用する。
SELECT
  CASE
    WHEN 1 THEN 1
    WHEN 1 THEN 123
    ELSE 'foo'
  END

-- 行列を転置をして集約を表現できるものの、実用的ではない。
-- 集約関数を使用する前にどのような二次元表が出力されるかを確認する。
SELECT id, name, price,
  CASE WHEN price BETWEEN 0 AND 1000 THEN '低額' ELSE NULL END AS "低額",
  CASE WHEN price BETWEEN 1001 AND 3000 THEN '中額' ELSE NULL END AS "中額",
  CASE WHEN price > 3000 THEN '高額' ELSE NULL END AS "高額"
FROM items;

SELECT 
  COUNT(CASE WHEN price BETWEEN 0 AND 1000 THEN '低額' ELSE NULL END) AS "低額",
  COUNT(CASE WHEN price BETWEEN 1001 AND 3000 THEN '中額' ELSE NULL END) AS "中額",
  COUNT(CASE WHEN price > 3000 THEN '高額' ELSE NULL END) AS "高額"
FROM items;

-- 単に価格帯ごとの合計金額を出力するのであれば以下が良い。
SELECT COUNT(*), CASE
  WHEN price BETWEEN 0 AND 1000 THEN '低額'
  WHEN price BETWEEN 1001 AND 3000 THEN '中額'
  ELSE '高額'
END AS price_rank
FROM items GROUP BY price_rank;
```

## 単純CASE式
プログラミング言語におけるswitch文とよく似ている。

```sql
SELECT
  CASE 'foo'
    WHEN 'foo' THEN 'foo'
    WHEN 'bar' THEN 'bar'
    WHEN 'baz' THEN 'baz'
    ELSE NULL
  END; -- foo

-- 比較対象が同一カラムの場合に、検索CASE式は冗長な記述となる。
SELECT
  CASE genre
    WHEN genre = '衣服' THEN 'A'
    WHEN genre = '事務用品' THEN 'B'
    WHEN genre = ''
```
