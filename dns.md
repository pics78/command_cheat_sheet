- DNSクライアントコマンド
    - nslookupコマンド
        - `$ nslookup [オプション] [ホスト名/ドメイン名/IPアドレス]`

        | オプション | 説明 |
        | --- | --- |
        | -type=レコード | リソースレコードタイプの指定 |
        | -norecurse | 再帰的な問い合わせをしない |

    - hostコマンド
        - `$ host [オプション] ホスト名/ドメイン名/IPアドレス [DNSサーバ]`

        | オプション | 説明 |
        | --- | --- |
        | -t タイプ | リソースレコードタイプ |
        | -v | 詳細表示 |

    - digコマンド
        - より詳細な情報を取得
        - `$ dig [@DNSサーバ] ホスト名/ドメイン名/IPアドレス [クエリタイプ]`

        | オプション | 説明 |
        | --- | --- |
        | -x | 指定IPアドレスの逆引き |
        | -p ポート番号 | 問い合わせ先のポート番号指定（デフォルトは53番） |

        | クエリタイプ | 説明 |
        | --- | --- |
        | a | ホスト名に対応するIPアドレス（デフォルト） |
        | ptr | IPアドレスに対するホスト名 |
        | ns | DNSサーバ |
        | mx | メールサーバ |
        | soa | SOAレコードタイプ |
        | hinfo | ホスト情報 |
        | axfr | ゾーン転送 |
        | txt | 任意の文字列 |
        | any | すべての情報 |

        - フラグ欄に表示されるフラグの種類

        | フラグ | 説明 |
        | --- | --- |
        | qr | 問い合わせに対する回答 |
        | aa | 権威のある回答 |
        | rd | 再帰検索を希望 |
        | ra | 再帰検索が可能 |

- DNSサーバ
    - BIND
        - サーバデーモン: `$ named`
        - /etc/named.confの設定

            | ステートメント | 説明 |
            | --- | --- |
            | acl | ACL（アクセス制御リスト）定義 |
            | controls | namedを操作できるホストの指定 |
            | include | 外部ファイルの読み込み |
            | key | 認証情報設定 |
            | options | namedの動作に関する詳細設定 |
            | zone | ゾーン定義 |

            - 定義済aclステートメント
                - any: 全てのIPアドレス
                - localhost: ローカルホストが使用しているIPアドレス
                - localnets: ローカルネットワークで使用しているIPアドレス
                - none: マッチしない

            - 主なoptionsステートメント

            | オプション | 説明 |
            | --- | --- |
            | directory ディレクトリパス; | ゾーンファイルを格納するディレクトリ |
            | datasize | データセグメントサイズの上限 |
            | coresize | コアファイルサイズの上限 |
            | max-cache-size キャッシュサイズ; | 最大キャッシュサイズ（Byte） |
            | recursion yes\|no; | 再帰的問い合わせを受け付けるか |
            | recursive-clients クライアント数; | 再帰的問い合わせの最大同時接続数 |
            | allow-query | 問い合わせを受け付けるホスト |
            | allow-recursion | 再帰的問い合わせを受け付けるホスト |
            | allow-transfer | ゾーン転送を許可するホスト |
            | allow-update | ゾーン情報の動的アップデートを受け付けるホスト |
            | blackhole | 問い合わせを受け付けないホスト |
            | forwarders { IPアドレス; } | 問い合わせの回送先DNSサーバ |
            | forward only\|first | forwardersの回送方法 |
            | notify yes\|no; | ゾーンデータの更新をセカンダリサーバに通知するか |
            | version | バージョン表示 |

            - zoneステートメントで指定する主なタイプ

            | タイプ | 意味 |
            | --- | --- |
            | hint | ルートDNSサーバを指定 |
            | master | プライマリDNSサーバ |
            | slave | セカンダリDNSサーバ |

        - named.rootを最新に更新: `$ dig @m.root-servers.net. ns > /var/named/named.root`
        - named.confの構文チェック: `$ named-checkconf named.confファイルのパス`
        - chrootをしている場合の構文チェック: `$ named-checkconf -t chrootディレクトリ named.confファイルのパス`
        - rndcコマンド
            - 主なサブコマンド

            | サブコマンド | 説明 |
            | --- | --- |
            | stop | namedの終了 |
            | refresh ゾーン | ゾーンデータベースのリフレッシュ |
            | stats | 統計情報を/var/named/named.statsに書き出す |
            | status | namedのステータス表示 |
            | dumpdb | キャッシュの内容をファイルに出力 |
            | reload ドメイン | 指定ゾーンファイルを再読み込み |
            | halt | namedを直ちに停止 |