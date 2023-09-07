# MySQL インデックス管理

## インデックス追加
```sql
-- 単一カラムインデックス
-- CREATE INDEXは単にインデックスを追加する場合に使用する。
CREATE INDEX pmacho_table_created_at_index ON pmacho_table (created_at);
-- ALTER TABLE ADD INDEXは他のテーブルの変更とともにインデックスを追加する場合に使用する。
ALTER TABLE pmacho_table
ADD COLUMN created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
ADD INDEX pmacho_table_created_at_index (created_at);

-- 複合カラムインデックス
-- カラムの順序に注意する。姓名の辞書で苗字で引いてからさらに名前で引くのと同じ仕組み。
CREATE INDEX pmacho_table_last_name_first_name_index ON pmacho_table (last_name, first_name);

-- ユニークインデックス
CREATE UNIQUE INDEX pmacho_table_name_unique_index ON pmacho_table (name);

-- テーブル作成時にインデックスを追加できる。
CREATE TABLE pmacho_table (
  id INT AUTO_INCREMENT,
  name VARCHAR(32),
  PRIMARY KEY (id),
  INDEX pmacho_table_name_index (name)
);

-- カラム追加時にもインデックスを追加できる。
ALTER TABLE pmacho_table
ADD COLUMN created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP(),
ADD INDEX pmacho_table_created_at_index (created_at);
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