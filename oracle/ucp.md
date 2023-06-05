# UCP
{:.no_toc}

* toc
{:toc}

## 公式ドキュメント
- [Universal Connection Pool Developer's Guide](https://docs.oracle.com/en/database/oracle/oracle-database/19/jjucp/index.html){:target="_blank"} ([日本語版](https://docs.oracle.com/cd/F19136_01/jjucp/index.html){:target="_blank"})

## 依存関係の追加
```xml
<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ojdbc10</artifactId>
    <version>${oracle.version}</version>
</dependency>

<dependency>
    <groupId>com.oracle.database.jdbc</groupId>
    <artifactId>ucp</artifactId>
    <version>${oracle.version}</version>
</dependency>

<!-- Optional -->
<dependency>
    <groupId>com.oracle.database.ha</groupId>
    <artifactId>ons</artifactId>
    <version>${oracle.version}</version>
</dependency>

<!-- Spring Bootの場合、デフォルトのDBCPであるHikariCPを取り除く -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <exclusions>
        <exclusion>
            <groupId>com.zaxxer</groupId>
            <artifactId>HikariCP</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## 

## ONSサブスクリプション確立の確認

ONSがクラスパスにない場合、アプリケーション起動時に以下のログが出力される。

```
DEBUG oracle.ucp : :::ons.jar is not on the classpath, FAN is disabled, java.lang.ClassNotFoundException: oracle.ons.ONS
```

ONSがクラスパスにあるがOracleデータベースがシングル構成の場合は以下の通り。

```
DEBUG oracle.ucp : :::Single-instance 12.x DB, FAN is heuristically disabled
```

ONSがクラスパスにあるがONSサブスクリプションを確立できなかった場合は以下の通り。

```
DEBUG oracle.ucp : :::Heuristically determine whether to enable FAN
DEBUG oracle.ucp : :::RAC/GDS 12.x, FAN is heuristically enabled
WARN  oracle.ucp : :::failed to start ONS: Subscription time out
```

ONSがクラスパスにありONSサブスクリプションを確立できた場合は以下の通り。

```
DEBUG oracle.ucp : :::Heuristically determine whether to enable FAN
DEBUG oracle.ucp : :::RAC/GDS 12.x, FAN is heuristically enabled
```
