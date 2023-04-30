# Windows Bits and Pieces

## hostsファイルの場所
```
C:\Windows\System32\drivers\etc\hosts
```

## resolv.confファイルの場所
Windowsに`resolv.conf`はなく、`netsh`コマンドで設定する。  
以下すべて管理者権限で発行する。

```powershell
# DNSサーバーの設定状況を確認する
netsh interface ip show dnsservers name="イーサネット"

# DHCPで取得する
netsh interface ipv4 set dnsservers name="イーサネット" source=dhcp

# プライマリDNSサーバーを設定する（ex. Google）
netsh interface ipv4 set dns name="イーサネット" source=static addr="8.8.8.8"

# セカンダリDNSサーバーを設定する（ex. Cloudflare）
netsh interface ipv4 add dns name="イーサネット" addr="1.1.1.1" index=2
```

## Cisco AnyConnect VPNプロファイルの場所
参考：[AnyConnect Client Download and Deployment | Cisco Meraki](https://documentation.meraki.com/MX/Client_VPN/AnyConnect_on_the_MX_Appliance/Client_deployment)
```
%ProgramData%\Cisco\Cisco AnyConnect Secure Mobility Client\Profile
```
