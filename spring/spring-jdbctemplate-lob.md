# Spring JdbcTemplate BLOB/CLOBの取り扱い（LobHandler / RowMapper）
{:.no_toc}

* toc
{:toc}

## テーブル定義
```sql
CREATE TABLE pmacho_lob (
  id INT,
  blob_data BLOB,
  clob_data CLOB,
  PRIMARY KEY (id)
);
```

## LobHandlerインターフェースをBean定義
バイナリを文字列などにシリアライズするLobHandlerユーティリティをBean定義する。

```java
@Bean
public LobHandler lobHandler() {
    return new DefaultLobHandler();
}
```

## RowMapperでLobHandlerを使用してバイナリをシリアライズ
```java
jdbcTemplate.update(
        "INSERT INTO pmacho_lob VALUES (1, ?, ?)",
        "blob".getBytes(),
        "clob"
);

// [{clob_data=clob, blob_data=blob, id=1}]
jdbcTemplate.query(
        "SELECT * FROM pmacho_lob;",
        new RowMapper<Map<String, Object>>() {
            @Override
            public Map<String, Object> mapRow(ResultSet rs, int rowNum) throws SQLException {
                Map<String, Object> row = new HashMap<>();
                row.put("id", rs.getInt("id"));
                row.put("blob_data", new String(lobHandler.getBlobAsBytes(rs, "blob_data")));
                row.put("clob_data", lobHandler.getClobAsString(rs, "clob_data"));

                return row;
            }
        }
);

// ラムダ式で表現してもよい。
jdbcTemplate.query(
        "SELECT * FROM pmacho_lob;",
        (rs, rowNum) -> {
            Map<String, Object> row = new HashMap<>();
            row.put("id", rs.getInt("id"));
            row.put("blob_data", new String(lobHandler.getBlobAsBytes(rs, "blob_data")));
            row.put("clob_data", lobHandler.getClobAsString(rs, "clob_data"));

            return row;
        }
);
```
