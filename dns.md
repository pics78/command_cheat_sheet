- DNSクライアントコマンド
    - nslookupコマンド（現在は非推奨->dig）
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

        | namedのオプション | 説明 |
        | --- | --- |
        | -t ディレクトリ | namedのルートディレクトリを指定（chroot環境） |
        | -u ユーザ名 | 実行ユーザ指定 |
        | -g グループ名 | 実行グループ指定 |

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
            | forwarders { IPアドレス; } | 問い合わせの転送先DNSサーバ |
            | forward only\|first | 転送失敗時の動作設定（only:自分で問い合わせしない, first: 自分で問い合わせする） |
            | notify yes\|no; | ゾーンデータの更新をセカンダリサーバに通知するか |
            | version | BINDのバージョンの問い合わせに対しての出力文字列を指定 |

            - zoneステートメントで指定する主なタイプ

            | タイプ | 意味 |
            | --- | --- |
            | hint | ルートDNSサーバを指定 |
            | master | プライマリDNSサーバ |
            | slave | セカンダリDNSサーバ |

        - ゾーンファイルをテキスト形式にする場合: zoneステートメントに `master-format text;` を記述（BIND9.9より）

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
        
        - ゾーンファイル管理
            - 種類
                - hint情報ファイル: ルートDNSサーバの一覧
                - 正引きファイル: ホスト名からIPアドレスへの対応を記述
                - 逆引きファイル: IPアドレスからホスト名への対応を記述
            - 書式
                - `名前 [TTL値] IN リソースレコードタイプ 値`
            - 主なディレクティブ
                - `$ORIGIN ドメイン名`: ホスト名のみの場合に補完するドメイン名
                - `$TTL 秒`: デフォルトTTLを指定（TTL: TimeToLive, 有効期間）
            - リソースレコードタイプ一覧

            | リソースレコードタイプ | 説明 |
            | --- | --- |
            | SOA（Start of Authority） | 管理情報（必ず最初に記載） |
            | NS(Name Server)  | ゾーンを管理するDNSサーバ |
            | MX（Mail Exchange） | メールサーバ |
            | A（Adress） | ホスト名に対応するIPアドレス |
            | AAAA | ホスト名に対応するIPv6アドレス |
            | CNAME（Canonical Name） | ホスト名の別名に対応するホスト名 |
            | PTR（Pointer） | IPアドレスに対応するホスト名 |

            - 構文チェック: `$ named-checkzone [オプション] ゾーン名 ゾーンファイル名`

            | オプション | 説明 |
            | --- | --- |
            | -t ディレクトリ | chrootしている場合はそのディレクトリを指定 |
            | -w ディレクトリ | ゾーンファイルを置いているディレクトリを指定 |

            - バイナリ形式からテキスト形式に変換（セカンダリDNSサーバ）: `$ named-compilezone`

        - セキュリティ
            - 公開鍵と秘密鍵の作成: dnssec-keygenコマンド
                - DNSSEC（DNS Security Extensions）  
                `$ dnssec-keygen [オプション] -n zone Zone名`
                - TSIG（Transaction Signature）  
                `$ dnssec-keygen [オプション] -n HOST 鍵の名前`

            | オプション | 説明 |
            | --- | --- |
            | -a | 暗号化アルゴリズム（デフォルト: RSASHA1） |
            | -b | 鍵のビット長指定 |
            | -n | 鍵のオーナータイプ指定 |

            - ゾーンファイルへの署名: dnssec-signzoneコマンド  
            `$ dnssec-signzone [オプション] ゾーンファイル`

            | オプション | 説明 |
            | --- | --- |
            | -S | 鍵ファイルの自動探索モード |
            | -K ディレクトリ | 鍵ファイルのあるディレクトリ指定 |
            | -o ゾーン名 | ゾーンの起点指定 |
