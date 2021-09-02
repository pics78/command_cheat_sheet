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