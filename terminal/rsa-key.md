# RSA秘密/公開鍵の作成方法

## RSA秘密鍵の作成
```
openssl genpkey -algorithm RSA -out pmacho.pem -pkeyopt rsa_keygen_bits:2048

ls -la
-rw-r--r--@ 1 hainet50b  staff  1704 Jul 24 18:57 pmacho.pem
```

## 対応するRSA公開鍵の作成
```
openssl rsa -pubout -in pmacho.pem -out pmacho-pub.pem

ls -la
-rw-r--r--@ 1 hainet50b  staff   451 Jul 24 19:01 pmacho-pub.pem
-rw-r--r--@ 1 hainet50b  staff  1704 Jul 24 18:57 pmacho.pem
```

RSA公開鍵は以下のようなフォーマットとなる。
```
cat pmacho-pub.pem
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA6YoU9NjsF4q1Sl1fn5qX
+fB6JtIVnx6FAUaeN3JDvzAPjcPQDkocTqwCi+YycJ9elIfw4Npvy2W2L+nSnDoW
mg4486yP0u9hX0Dd38U/Jg2U5SgGq6Q/HxLezmPDVaZkJp9MoqJJT3xSPcz45ZX9
URpoLqY+X/u4WZnUJ/qPNgEQZ9u6Ni41tjoNSXWCaCto8r3ppU3vfz1/9vz7x/Sb
pv6c/ja4J/SVoxRy6rNC+XRVk6BPUDEXlhCNNpTUshyqxc8IOpCRs9oDjRn5h7JW
KiCp9YS6LLGNHWwnDtKT6jqvHjFJgTqRWidGeSrAwN1PCb0t0efz8KTO8W+0XWpw
DQIDAQAB
-----END PUBLIC KEY-----
```
