- DHCP
    - dhcpd.confの設定項目一覧

    | 設定項目 | 説明 |
    | --- | --- |
    | option domain-name ドメイン名; | ドメイン名 |
    | option domain-name-servers IPアドレス ; | DNSサーバ |
    | option routers IPアドレス; | デフォルトゲートウェイのIPアドレス |
    | option subnet-mask IPアドレス; | サブネットマスク |
    | option broadcast-address IPアドレス; | ブロードキャストアドレス |
    | option ntp-servers IPアドレス; | NTPサーバのIPアドレス |
    | option netbios-name-servers IPアドレス; | WINSサーバのIPアドレス |
    | option nis-domain ドメイン名; | NISドメイン名 |
    | option nis-servers IPアドレス; | NISサーバ |
    | default-lease-time | 期限を求められなかった場合のリース期限（秒） |
    | max-lease-time | 期限を求められた場合の最大リース期限（秒） |
    | range [dynamic-bootp] 開始アドレス [終了アドレス]; | 割り当てるIPアドレスの範囲|
    | dynamic-bootp | フラグがあればBOOTPクライアントへ割り当てる |
    | fixed-address IPアドレス; | クライアントに割り当てる固定IPアドレス（host内で指定） |
    | hardware インターフェースタイプ MACアドレス; | クライアントを特定するためのMACアドレス |
    | (allow\|deny) unknown-clients; | MACアドレス指定のないクライアントへのIPアドレス割り当て可否 |

    - DHCPクライアントの設定
        - CentOS (/etc/sysconfing/network-scripts/ifcfg-enp0s3): `BOOTPROTO=dhcp`
        - Ubuntu,Debian等 (/etc/network/interfaces): `iface enp0s3 inet dhcp`
        - NetworkManager: `$ nmcli con mod enp0s3 ipv4.method auto`
        - DHCPサーバからIPアドレスを取得し設定: `$ dhclient -d enp0s3`
    - DHCPリレーエージェント
        - enp0s6で受け取ったDHCPリクエストを別のDHCPサーバにリレーするようにして起動: `$ dhcrelay -i enp0s6 172.17.0.2`
