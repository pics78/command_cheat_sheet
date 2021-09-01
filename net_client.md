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
        - DHCPクライアントとして動作するデーモン
            - dhclient（通常はバックグラウンドプロセス。-dオプションでフォアグラウンドプロセスを強制）
                - IPアドレスのリリース: `$ dhclient -r`
                - IPアドレスの取得: `$ dhclient`
            - dhcpcd
                - IPアドレスのリリース: `$ dhcpcd -k`
                - IPアドレスの取得: `$ dhcpcd`
    - DHCPリレーエージェント
        - enp0s6で受け取ったDHCPリクエストを別のDHCPサーバにリレーするようにして起動: `$ dhcrelay -i enp0s6 172.17.0.2`
- LDAP
    - OpenLDAPサーバの主な設定・管理コマンド一覧

    | コマンド | 説明 |
    | --- | --- |
    | slapadd | LDAPデータベースにエントリを追加 |
    | slapcat | LDAPデータベースの内容をLDIFで出力 |
    | slapindex | LDAPデータベースのインデックスを再構築 | 
    | slappasswd | 暗号化されたパスワードを生成 |
    | slaptest | LDAPサーバの設定をチェック |

    - LDAPクライアントコマンド

    | コマンド | 説明 |
    | --- | --- |
    | ldapadd | エントリ追加 |
    | ldapsearch | エントリ検索 |
    | ldapdelete | エントリ削除 |
    | ldapmodify | エントリ削除 |
    | ldappasswd | パスワード変更 |

    - LDAPクライアントコマンド共通オプション

    | オプション | 説明 |
    | --- | --- |
    | -h ホスト | LDAPサーバを指定（未指定はローカルホスト） |
    | -H URI | LDAPサーバをURIで指定 |
    | -x | SASLを使わず簡易認証を用いる |
    | -p ポート番号 | LDAPのポートを指定（デフォルトは389） |
    | -D BINDDN | 認証に利用するDNを指定 |
    | -W | 認証時のパスワードを対話的に入力 |
    | -w パスワード | 認証時のパスワードを指定 |
    | -f ファイル名 | LDIFファイルを指定 |

    （DN: 識別名, Distinguished Name）

    - 検索: `$ ldapsearch [オプション] 検索条件[属性]`

    | オプション | 説明 |
    | --- | --- |
    | -b ベースDN | 検索を開始するベースDNを指定 |
    | -L | 検索結果をLDIFv1形式で表示 |
    | -LL | 検索結果をコメントなしの形式で表示 |
    | -LLL | 検索結果をコメントとLDAPバージョンなしで表示 |

    - 変更
        - LDIFファイルの書き方
            - 追加
            ```
            dn: 変更するDN
            changetype: modify
            add: 追加する属性名
            追加する属性名: 値
            ```
            - 修正
            ```
            dn: 変更するDN
            changetype: modify
            replace: 修正する属性名
            修正する属性名: 値
            ```
            - 削除
            ```
            dn: 変更するDN
            changetype: modify
            delete: 削除する属性名
            削除する属性名
            ```
    - パスワードの変更: `$ ldappasswd [オプション] ユーザエントリDN`

    | オプション | 説明 |
    | --- | --- |
    | -s パスワード | 新しいパスワードを指定 |
    | -S | 新しいパスワードを対話的に入力 |