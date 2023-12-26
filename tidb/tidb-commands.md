# よく使うTiDBコマンド

## 実行計画を取得する
EXPLAINに加えてより詳細にアクセスパスを表示するTRACE句が実装されている。

```sql
-- SQL標準
EXPLAIN SELECT * FROM emp;

-- TiDB独自実装
TRACE SELECT * FROM emp;
```