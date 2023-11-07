# SQL関数

## 文字列

### 文字列の連結（CONCAT）

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
        <code-block lang="sql">
        SELECT CONCAT('foo', 'bar'); -- foobar
        </code-block>
    </tab>
    <tab title="Oracle" group-key="Oracle">
        <code-block lang="sql">
        SELECT CONCAT('foo', 'bar') FROM DUAL; -- foobar
        SELECT 'foo' || 'bar' FROM DUAL; -- foobar
        </code-block>
    </tab>
</tabs>

### 文字列の長さ（LENGTH）

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
        桁数
        <code-block lang="sql">
        SELECT CHAR_LENGTH('value'); -- 5
        SELECT CHAR_LENGTH('あいうえお'); -- 5
        </code-block>
        バイト数
        <code-block lang="sql">
        SELECT LENGTH('value'); -- 5
        SELECT LENGTH('あいうえお'); -- 15
        </code-block>
    </tab>
    <tab title="Oracle" group-key="Oracle">
        桁数
        <code-block lang="sql">
        TODO
        </code-block>
        バイト数
        <code-block lang="sql">
        TODO
        </code-block>
    </tab>
</tabs>

### 文字列の繰り返し（REPEAT）

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
        <code-block lang="sql">
        SELECT REPEAT('foo', 3); -- foofoofoo
        </code-block>
    </tab>
    <tab title="Oracle" group-key="Oracle">
        <code-block lang="sql">
        SELECT RPAD('foo', LENGTH('foo') * 3, 'foo') FROM DUAL; -- foofoofoo
        </code-block>
    </tab>
</tabs>

### 文字列を逆順にする（REVERSE）

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
        <code-block lang="sql">
        SELECT REVERSE('abc'); -- cba
        </code-block>
    </tab>
    <tab title="Oracle" group-key="Oracle">
        <code-block lang="sql">
        SELECT REVERSE('abc') FROM DUAL; -- cba
        </code-block>
    </tab>
</tabs>

## 数値

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
    </tab>
    <tab title="Oracle" group-key="Oracle">
    </tab>
</tabs>

## ハッシュ・暗号

### UUID生成

<tabs group="rdbms">
    <tab title="MySQL" group-key="MySQL">
        <code-block lang="sql">
        SELECT UUID(); -- d7813791-7d4f-11ee-8628-0242ac110003
        </code-block>
    </tab>
    <tab title="Oracle" group-key="Oracle">
        <code-block lang="sql">
        SELECT SYS_GUID() FROM DUAL; -- 098D9FFAD8B40128E063020011AC143A
        </code-block>
    </tab>
</tabs>
