- パケットフィルタリング
    - テーブルとデフォルトチェイン一覧

    | テーブル | チェイン |
    | --- | --- |
    | filter | INPUT, OUTPUT, FORWARD |
    | nat | PREROUTING, POSTROUTING, OUTPUT |
    | mangle | INPUT, OUTPUT, PREROUTING, POSTROUTING |

    - iptablesコマンド
        - 書式
            - `$ iptables -[AD] チェイン ルール` (Add/Delete)
            - `$ iptables -P チェイン ターゲット` (Policy)
            - `$ iptables -[LFNX] チェイン` (List)
            - `$ iptables -I チェイン [ルール番号] ルール` (Insert)

        - サブコマンド

        | コマンド | 説明 |
        | --- | --- |
        | -A | チェインの最後にルールを追加 |
        | -D | ルールを削除 |
        | -P | ポリシーを変更 |
        | -L [チェイン] | ルールのリスト表示 |
        | -N チェイン | 指定した名前のユーザ定義チェインを作成 |
        | -X チェイン | 指定したユーザ定義チェインを削除 |
        | -I | ルール番号を指定してルールを挿入 |
        | -F チェイン | 指定したチェイン内のルールを全て削除 |

        - チェイン

        | チェイン | 説明 |
        | --- | --- |
        | INPUT | ホストに入ってくるパケット |
        | OUTPUT | ローカルマシンで生成されたパケット |
        | FORWARD | ホストを経由するパケット |
        | PREROUTING | 入ってきたパケットを変換 |
        | POSTROUTING | 出ていくパケットを変換 |

        - ターゲット

        | ターゲット | 説明 |
        | --- | --- |
        | ACCEPT | 許可 |
        | DROP | 破棄 |
        | REJECT | 拒否（送信元にも通知） |
        | MASQUERADE | 送信元IPアドレスとポート番号を変換 |
        | SNAT | 送信元IPアドレスの変換（source） |
        | DNAT | 送信先IPアドレスの変換（destination） |
        | LOG | ログ出力 |

        - ルール

        | ルール | 説明 |
        | --- | --- |
        | -s 送信元 | 送信元のIPアドレス |
        | -d 送信先 | 送信先のIPアドレス |
        | --sport ポート番号 | 送信元のポート番号 |
        | --dport ポート番号 | 送信先のポート番号 |
        | --to | 宛先指定（IPアドレス:ポート番号） |
        | -j ターゲット | 適用されるターゲット |
        | -p プロトコル | プロトコル（tcp, udp, icmp, all） |
        | -i インターフェース | 入力インターフェース |
        | -o インターフェース | 出力インターフェース |

        - オプション

        | オプション | 説明 |
        | --- | --- |
        | -v | 詳細表示 |
        | -n | IPアドレスやポート番号を数値で表示 |
        | --line-numbers | ルール番号を表示 |

        - 例
            - FORWARDチェインのポリシーをDROPに変更: `$ iptables -P FORWARD DROP`
            - 192.168.0.0/24からのICMPパケットを拒否: `$ iptables -A INPUT -p icmp -s 192.168.0.0/24 -j REJECT`
            - 80番ポート宛のTCPパケットが入ってきた時にログ出力する: `$ iptables -A INPUT -p tcp --dport 80 -j LOG --log-prefix "HTTP access..."`
            - 192.168.1.2の80番ポート宛のTCPパケット転送を許可: `$ iptables -A FORWARD -d 192.168.1.2 -p tcp --dport 80 -j ACCEPT`
            - 8080番ポートに届いたTCPパケットの送信先を192.168.1.2の80番ポートに変更: `$ iptables -t nat -A PREROUTING -p tcp --dport 8080 -i eth1 -j DNAT --to 192.168.1.2:80`
        - 設定の保存
            - `$ service iptables save` （RedHat系の古いディストリビューションの場合）
            - `$ iptables-save > /etc/sysconfig/iptables`
            - `$ iptables-save > /etc/iptables/rules.v4`
        - 設定の復元
            - `$ iptables-restore < /etc/sysconfig/iptables`

    - ip6tablesコマンド  
    IPv6用のコマンド.使い方はiptablesと同じ.

    - ルータ構成
        - ルーティングテーブルの表示
            - `$ route`
            - `$ netstat -r`
            - `$ ip route`
        - パケット転送設定
            - IPv4: `$ echo 1 > /proc/sys/net/ipv4/ip_forward`
            - IPv6: `$ echo 1 > /proc/sys/net/ipv6/conf/all/forwarding`
            - 恒久的な設定
                - `/etc/sysctl.conf` ファイルに適切なエントリの記述
                - （Red Hat系）`/etc/sysconfig/network` ファイルに `FORWARD_IPv4=yes` または `IPV6FORWARDING=yes` と記述
            - IPマスカレード
                - `$ iptables -t nat -A POSTROUTING -o enp0s6 -j MASQUERADE`
                - WAN側IPアドレスが固定の場合: `$ iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o enp0s6 -j SNAT 10.1.2.3`

    - firewalld  
    CentOSでのファイヤーウォールの仕組み
        - 開始: `$ systemctl start firewalld.service`
        - 設定: firewall-cmdコマンド  
        `$ firewall-cmd オプション [--zone=ゾーン名]`

        | オプション | 説明 |
        | --- | --- |
        | --state | 状態の表示 |
        | --reload | 設定の再読み込み |
        | --permanent | 永続設定をする |
        | --get-zones | 定義されているゾーンを一覧表示 |
        | --get-active-zones | インターフェースが属しているゾーンを表示 |
        | --get-default-zone | デフォルトゾーンを表示 |
        | --set-default-zone | デフォルトゾーンを変更 |
        | --get-services | 定義されているサービスを一覧表示 |
        | --list-all | ゾーンの設定を表示 |
        | --list-services | 指定したゾーンで有効なサービスを全て表示 |
        | --add-service=サービス名 | 指定したサービスを有効にする |
        | --add-ports=ポート番号/tcp・udp | 指定したポート番号を許可する |
        | --remove-service=サービス名 | 指定したサービスを無効にする |
        | --remove-port=ポート番号/tcp・udp | 指定したポートを拒否する |
        | --change-interface=インターフェース | インターフェースが属するゾーンの変更 |
        | --zone=ゾーン | ゾーンを指定（デフォルトはpublic） |
    
    - ufwコマンド  
    Ubuntuでのファイヤーウォール設定コマンド  
    `$ ufw サブコマンド`

    | サブコマンド | 説明 |
    | --- | --- |
    | enable | 有効化 |
    | disable | 無効化 |
    | status | 状態を表示 |
    | status numbered | 状態をルール番号とともに表示 |
    | status verbose | 状態を詳細に表示 |
    | allow サービス名\|ポート番号\|プロトコル\|IPアドレス | 許可する通信を設定 |
    | delete ルール\|ルール番号 | 指定したルールを削除 |
    | insert ルール番号 ルール | ルール番号を指定してルールを追加 |
    | default allow\|deny\|reject [incoming\|outgoing\|routed] | デフォルトのポリシーを設定 |
    | app list | 指定可能なアプリの一覧表示 |
    | reset | 何もルールが設定されていない状態に戻して無効化 |

        - 変更を反映せずに確認するためのオプション: --dry-run

        - 例
            - 外部からの接続のデフォルトのポリシーをdenyに設定  
            `$ sudo ufw default deny incoming`
            - HTTP接続を許可  
            `$ sudo ufw allow http`
            - 192.168.1.0/24の範囲からのアクセスを許可  
            `$ sudo ufw allow from 192.168.1.0/24`
        
        - 設定したルールの保存先
            - IPv4: `/etc/ufw/user.rules`
            - IPv6: `/etc/ufw/user6/rules`

- OpenSSH
    - SSHの設定
        - デーモン: sshd
        - 設定ファイル: `/etc/ssh/sshd_config`

        | sshd_configの設定項目 | 説明 |
        | --- | --- |
        | Port | SSHで使うポート番号（デフォルトは22） |
        | Protocol | 利用するSSHのバージョン |
        | ListenAddress | 接続受付アドレス（0.0.0.0で全て受け付ける） |
        | HostKey | ホスト鍵ファイルのパス |
        | SyslogFacility | ログのファシリティ |
        | LogLevel | ログのプライオリティ |
        | LoginGraceTime | ログインを試みることができる最大時間（秒） |
        | PermitRootLogin | rootログインを許可するかどうか |
        | StrictModes | パーミッションを厳密にチェックするかどうか |
        | MaxAuthTries | 認証を試みることができる最大回数 |
        | PubkeyAuthentication | 公開鍵認証を許可するかどうか |
        | AuthorizedKeysFile | 公開鍵を登録するファイル |
        | PasswordAuthentication | パスワード認証を許可するかどうか |
        | PermitEmptyPasswords | 空パスワードを許可するかどうか |
        | UsePAM | PAMを使用する |
        | X11Forwarding | X11フォワーディングの有効化/無効化 |
        | AllowUsers | ログインを許可するユーザ |
        | DenyUsers | ログインを禁止するユーザ |
        | AllowGroups | ログインを許可するグループ |
        | DenyGroups | ログインを禁止するグループ |
        | Subsystem | 外部サブシステムを指定 |

    - 公開鍵認証
        - 公開鍵と秘密鍵のペアの作成: ssh-keygenコマンド  
        `$ ssh-keygen [オプション]`

        | オプション | 説明 |
        | --- | --- |
        | -f ファイル名 | 鍵ファイルを指定 |
        | -t | 暗号化方式を指定 |
        | -p | 既存の鍵ファイルのパスフレーズを変更 |
        | -P パスフレーズ | 古いパスフレーズを指定 |
        | -N パスフレーズ | 新しいパスフレーズを指定 |
        | -l | フィンガープリントを表示 |
        | -R ホスト名 | 指定されたホストの鍵をknown_hostsファイルから削除 |

        | 暗号化方式 | 説明 |
        | --- | --- |
        | -t rsa1 | SSHバージョン1のRSA鍵 |
        | -t dsa | SSHバージョン2のDSA鍵 |
        | -t rsa | SSHバージョン2のRSA鍵 |
        | -t ecdsa | SSHバージョン2のECDSA鍵 |
        | -t ed25519 | SSHバージョン2のED25519鍵 |