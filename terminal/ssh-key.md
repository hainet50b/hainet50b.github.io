# SSH鍵の作成

```shell
ssh-keygen -t rsa -b 2048 -C '20400616+hainet50b@users.noreply.github.com' -f ~/.ssh/pmacho-key

ls -la ~/.ssh
-rw-------@  1 hainet50b  staff  1864 Aug 31 21:01 pmacho-key
-rw-r--r--@  1 hainet50b  staff   425 Aug 31 21:01 pmacho-key.pub
```

公開鍵は以下のような形式となる。
```shell
cat ~/.ssh/pmacho-key.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDboJAKD4QONdsVKx7FKZnFvaHsNJsjxTZdDQRgJH6J5jL9K/F2OXJNoQSwAfnVj3GT3+Z5OCYc7822gkZbNcbNsuI9XzRkPseESWpO7cGxg2oCdZa/7LZP097U0GQkC4GGEV7qeZgw7NZNvt7WVFjbLGTk8knM562axmWSxVLQ4yFVEOp4/Seg+oOIcicDHSRkuzLh18uaqx0Lh9koxEKTf+9aLWBP6gtZNb14kgtp1EUSDKLeKzyMr07NDpu2gckOhq6JTFDDbWvXKpTPaU75ETxtX0kopxjsl8lvKe254t8qQzwYxSToaOPU+ANMBPfQqxDSIYudG83Jr8oTBfsv 20400616+hainet50b@users.noreply.github.com
```

末尾のメールアドレスは実務では`-C ''`などとして空白とすることがある。
