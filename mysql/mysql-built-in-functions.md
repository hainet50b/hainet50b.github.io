# MySQL SQL関数
{:.no_toc}

* toc
{:toc}

MySQL SQL関数一覧：[SQL関数および演算子リファレンス \| MySQL 8.0 リファレンスマニュアル](https://dev.mysql.com/doc/refman/8.0/ja/built-in-function-reference.html){:target="_blank"}

## 文字列
```sql
-- UUID
SELECT UUID(); -- 32a50e2c-7797-11ee-a2dc-0242ac110002

-- 文字列の長さ
SELECT LENGTH('value'); -- 5
-- 文字列連結
SELECT CONCAT('foo', 'bar'); -- foobar
SELECT CONCAT('foo', 'bar', 'baz'); -- foobarbaz
-- 繰り返し
SELECT REPEAT('foo', 3); -- foofoofoo
-- 逆順
SELECT REVERSE('abc'); -- cba

-- 大文字・小文字に変換
SELECT UPPER('foo'); -- FOO
SELECT LOWER('FOO'); -- foo

-- 切り出し
SELECT LEFT('abcde', 3); -- abc
SELECT RIGHT('abcde', 3); -- cde
-- パディング
SELECT LPAD('123', 5, '0'); -- 00123
SELECT LPAD('123', 1, '0'); -- 1
SELECT RPAD('123', 5, '0'); -- 12300
-- 空白文字の削除
SELECT LTRIM('   foo   '); -- 'foo   '
SELECT RTRIM('   foo   '); -- '   foo'

-- 16進数表現
SELECT HEX('foo'); -- 666F6F
SELECT CAST(UNHEX('666F6F') AS CHAR); -- foo
```

## 数値
```sql
-- 絶対値
SELECT ABS(-1); -- 1
-- 余り
SELECT MOD(5, 2); -- 1
-- 累乗
SELECT POW(2, 10); -- 1024
-- 平方根
SELECT SQRT(4); -- 2

-- ランダム
SELECT RAND(); -- 0 <= x < 1
-- ランダムな整数
SELECT FLOOR(RAND() * 10); -- 0 ~ 9

-- 切り上げ
SELECT CEIL(1.1); -- 2
-- 切り捨て
SELECT FLOOR(1.9); -- 1

-- 桁数調整
SELECT FORMAT(1.12345, 3); -- 1.123
SELECT FORMAT(1.1, 3); -- 1.100
SELECT FORMAT(1.1, 0); -- 1
```

## 論理条件
```sql
-- 値の範囲
SELECT 2 BETWEEN 1 AND 3; -- 1
-- 大きい方を選択
SELECT GREATEST(1, 2); -- 2
SELECT GREATEST(
  (SELECT 1),
  (SELECT 2)
); -- 2
-- 小さい方を選択
SELECT LEAST(1, 2); -- 1
TODO
```

## 時間間隔
時間間隔一覧：[時間間隔 \| MySQL 8.0 リファレンスマニュアル](https://dev.mysql.com/doc/refman/8.0/ja/expressions.html#temporal-intervals){:target="_blank"}

## 日時
```sql
-- 現在日時
SELECT NOW(); -- 現在日時
-- 現在日付
SELECT DATE(NOW()); -- 現在日付

-- 日時加算
SELECT DATE_ADD('2000-01-01 00:00:00', INTERVAL 1 YEAR); -- 2001年
SELECT DATE_ADD('2000-01-01 00:00:00', INTERVAL 1 DAY); -- 1月2日
SELECT DATE_ADD('2000-01-01 00:00:00', INTERVAL 1 HOUR); -- 1時
SELECT DATE_ADD('2000-01-01 00:00:00', INTERVAL 1 MINUTE); -- 0時1分
SELECT DATE_ADD('2000-01-01 00:00:00', INTERVAL 1 SECOND); -- 0時0分1秒
-- 日時減算
SELECT DATE_SUB('2000-01-01 00:00:00', INTERVAL 1 YEAR); -- 1999年

-- 日付フォーマット
SELECT DATE_FORMAT('2000-01-01 00:00:00', '%Y-%m-%dT%H%i%s.%f'); -- 2000-01-01T00:00:00.000000
```

## ユーザー・データベース
```sql
-- 現在のユーザー
SELECT CURRENT_USER(); -- root@%
SELECT USER(); -- root@172.17.0.1
-- 現在のデータベース
SELECT DATABASE(); -- pmacho_db
```

## 暗号計算
```sql
-- AES暗号化
SELECT HEX(AES_ENCRYPT('value', 'key')); -- 0x0AF080B9B33829908326665FA9FB7BC5
-- AES復号
SELECT CAST(AES_DECRYPT(UNHEX('0AF080B9B33829908326665FA9FB7BC5'), 'key') AS CHAR); -- value

-- MD5
SELECT MD5('foo'); -- acbd18db4cc2f85cedef654fccc4a4d8
-- SHA256
SELECT SHA2('foo', 256); -- 2c26b46b68ffc68ff99b453c1d30413413422d706483bfa0f98a5e886266e7ae
```
