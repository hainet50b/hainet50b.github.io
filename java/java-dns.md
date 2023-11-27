# Java 名前解決

nslookupコマンドと同等の結果をJavaで取得する。

```shell
nslookup pages.programacho.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
pages.programacho.com	canonical name = hainet50b.github.io.
Name:	hainet50b.github.io
Address: 185.199.108.153
Name:	hainet50b.github.io
Address: 185.199.109.153
Name:	hainet50b.github.io
Address: 185.199.110.153
Name:	hainet50b.github.io
Address: 185.199.111.153
```

```java
try {
    InetAddress[] ips = InetAddress.getAllByName("pages.programacho.com");

    for (InetAddress ip : ips) {
        System.out.println(ip);
    }
} catch (UnknownHostException e) {
    throw new RuntimeException(e);
}

pages.programacho.com/185.199.111.153
pages.programacho.com/185.199.108.153
pages.programacho.com/185.199.109.153
pages.programacho.com/185.199.110.153
```