# MySQL インデックス管理

## インデックス追加
```sql
-- 単一カラムインデックス
ALTER TABLE pmacho_table ADD INDEX pmacho_table_created_at_index (created_at);

-- 複合カラムインデックス
-- カラムの順序に注意する。姓名の辞書で苗字で引いてからさらに名前で引くのと同じ仕組み。
ALTER TABLE pmacho_table ADD INDEX pmacho_table_last_name_first_name_index (last_name, first_name);

-- ユニークインデックス
ALTER TABLE pmacho_table ADD UNIQUE pmacho_table_name_unique_index (name);
```

## インデックス確認
```sql
SHOW INDEX FROM pmacho_table;

-- 複数のデータベースやテーブルを確認できる。
SELECT
  table_name,
  index_name,
  column_name,
  non_unique,
  index_type,
  seq_in_index,
  collation
FROM
  information_schema.statistics
WHERE
  TABLE_SCHEMA = 'pmacho_db'
  AND TABLE_NAME = 'pmacho_table';
```

## インデックス削除
```sql
ALTER TABLE pmacho_table DROP INDEX pmacho_table_created_at_index;
```