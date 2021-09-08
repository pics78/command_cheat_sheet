- Apache HTTPサーバ
    - インストール
        - configureスクリプトの主なオプション

        | オプション | 説明 |
        | --- | --- |
        | --prefix=ディレクトリ | インストール先ディレクトリ指定 |
        | --sysconfdir=ディレクトリ | 設定ファイルのディレクトリ指定 |
        | --enable-module=モジュール | 標準モジュールを組み込む |
        | --disable-module=モジュール | 標準モジュールを組み込まない |
        | --enable-shared=モジュール | 標準モジュールをDSOで組み込む |
        | --disable-shared=モジュール | 標準モジュールをDSOで組み込まない |

        - CentOS等: `$ yum install httpd`
        - Ubuntu等: `$ apt install apache2`

        - 主なモジュール

        | モジュール名 | 説明 |
        | --- | --- |
        | mod_access_compat | ホストベースのアクセス制御 |
        | mod_alias | エイリアス機能の利用 |
        | mod_authz_host | ホストベースのアクセス制御（Requireディレクティブ） |
        | mod_authz_user | ユーザベースのアクセス制御（Requireディレクティブ） |
        | mod_authz_core | 認可（アクセス制御）コア機能 |
        | mod_auth_basic | 基本認証 |
        | mod_auth_digest | ダイジェスト認証 |
        | mod_authn_core | 認証コア機能 |
        | mod_authn_file | .htaccessファイルを使ったユーザ認証 |
        | mod_cgi | CGIスクリプトの実行 |
        | mod_dir | ディレクトリのインデックス表示 |
        | mod_http2 | HTTP/2 |
        | mod_info | サーバの設定情報表示 |
        | mod_log_config | リクエストログの生成 |
        | mod_mime | 拡張子に応じたファイルタイプの判定 |
        | mod_negotiation | クライアントごとの自動判別機能 |
        | mod_perl | Perlの利用 |
        | mod_php | PHPの利用 |
        | mod_proxy | プロキシ機能 |
        | mod_rewrite | リダイレクト処理 |
        | mod_so | DSO機能 |
        | mod_ssl | SSL/TLS機能 |
        | mod_status | サーバのステータス表示 |
        | mod_unixd | 実行ユーザ・グループの指定やchroot機能 |
        | mod_userdir | ユーザディレクトリの公開 |
        | mod_version | バージョン依存の設定 |

    - 起動と制御
        - 起動
            - CentOS等: `$ systemctl start httpd.service`
            - Ubuntu等: `$ systemctl start apache2.service`
        - apachectlコマンド（Debian系ではapache2ctl）
            - 書式: `$ apachectl サブコマンド`

            | サブコマンド | 説明 |
            | --- | --- |
            | start | 起動 |
            | stop | 終了 |
            | restart | 再起動 |
            | graceful | 安全に再起動（リクエスト処理中の場合は完了を待つ） |
            | reload | 設定ファイルの再読み込み |
            | configtest | 設定ファイルの構文チェック |
    
        - httpdコマンド
            - 書式 `$ httpd [オプション ]

            | オプション | 説明 |
            | --- | --- |
            | -l | 静的に組み込まれたモジュールの表示 |
            | -M | 静的、動的に組み込まれたモジュールを両方表示（Apache2.2系から） |

    - httpd.confの主な設定値
        - ServerTokens Prod|Major|Minor|Min|OS|Full  
        右にいくほど詳細なバージョン情報を公開（通常はProd）
        - ServerRoot パス  
        httpdの利用するトップディレクトリ
        - ServerName サーバのホスト名[:ポート番号]
        - ServerAdmin 管理者のメールアドレス  
        エラーページなどに表示されるようになる
        - ServerAlias サーバの別名（VirtualHost）
        - StartServers 起動時の子プロセス数
        - MinSpareServers 待機子プロセス数の最小値
        - MaxSpareServers 待機子プロセス数の最大値
        - ServerLimits 生成子プロセスの設定可能上限
        - MaxConnectionsPerChild リクエスト数 (Ver2.3.9以前はMaxRequestsPerChild)  
        httpd子プロセスが処理するリクエストの最大数
        - MaxRequestWorkers 同時接続数
        - Timeout タイムアウト時間[s]
        - KeepAlive on|off （TCP接続保持の有効化）
        - MaxKeepAliveRequests 上限リクエスト数
        - KeepAliveTimeout タイムアウト時間
        - Listen [IPアドレス:]ポート番号（デフォルトは80番ポート）
            - IPv4の例: `Listen 192.168.0.1:8080`
            - IPv6の例: `Listen [2001:db8::1]:8080`
        - User 子プロセス実行ユーザ名
        - Group 子プロセス実行グループ名
        - DocumentRoot ディレクトリ
        - UserDir 一般ユーザの公開ディレクトリ|disabled
        - DirectoryIndex インデックスファイル名
        - ErrorLog ログファイルのパス
        - LogLevel ログレベル
        - LogFormat "フォーマット" 書式名

        | 主なフォーマット文字列 | 説明 |
        | --- | --- |
        | %a | リモートIPアドレス |
        | %A | ローカルIPアドレス |
        | %b | クライアントに送信されたHTTPヘッダ以外のバイト数 |
        | %f | ファイル名 |
        | %h | リモートホスト（クライアント） |
        | %H | リクエストプロトコル |
        | %l | リモートログ名 |
        | %m | リモートメソッド |
        | %r | リクエストの最初の行 |
        | %t | リクエスト処理を終えた時間 |
        | %T | リクエスト処理にかかった時間 |
        | %u | リクエストしたユーザ名 |
        | %U | リクエストされたURL |
        | %s | クライアントに返すステータスコード |
        | %{Referer}i | リクエスト内のRefererヘッダの内容 |
        | %{User-agent}i | リクエスト内のUser-agentヘッダの内容 |

        - CustomLog ログファイル名 ログ書式
        - HostnameLookups on|off
        - Alias アクセスディレクトリ 参照先のパス
        - Redirect [ステータス] DocumentRootからのパス(/..) 転送先URL(http://..)  
        ステータスはデフォルトで302(temp)または301(permanent)
        - ScriptAlias 指定ディレクトリ CGI格納ディレクトリ
        - ErrorDocument エラーコード ファイル名|文字列|URL
        - Options オプション

        | 主なオプション | 説明 |
        | --- | --- |
        | ExecCGI | cgi-binディレクトリ以外でCGIプログラムを実行できる |
        | Includes | SSIを許可 |
        | Indexes | DirectoryIndexで指定されたファイルがない場合にファイル名一覧を生成 |
        | FollowSymLinks | リンク先ファイルを参照 |
        | ALL | すべてのオプションを利用 |
        | None | すべてのオプションを無効 |

        - AccessFileName 外部設定ファイル名（通常は.htaccess）
        - AllowOverride パラメータ  
        外部設定ファイルの利用に関する設定

        | パラメータ | 説明 |
        | --- | --- |
        | AuthConfig | 認証に関する設定を有効化 |
        | Indexes | DirectoryIndexなどの設定を有効化 |
        | FileInfo | ファイルタイプの制御設定を有効化 |
        | Limit | Order, Allow, Denyによる設定を有効化 |
        | Options | Optionsの設定を有効化 |
        | None | .htaccessでの変更を無効化 |
        | All | .htaccessで変更可能なすべての設定を有効化 |

        - クライアントアクセス認証関連
            - AuthType Basic|Digest
            - AuthName 認可領域名  
            認証時のダイアログボックスに表示される文字列
            - AuthUserFile パスワードファイル名
            - AuthGroupFile 認証するグループファイル名（Basic認証）
            - AuthDigestGroupFile 認証するグループファイル名（Digest認証）
            - Require 認証対象とするユーザまたはグループ
                - `Require user ユーザ名 ユーザ名 ...`
                - `Require group グループ名 グループ名 ...`
                - `Require valid-user` （パスワードファイルにエントリのあるすべてのユーザが認証対象となる）

        - 適用範囲の指定
            - ファイルごとの設定: `<Files ファイル名>...</Files>`
            - ディレクトリごとの設定: `<Directory ディレクトリ名>...</Directory>`
            - URLごとの設定: `<Location URL>...</Location>`
            - モジュールごとの設定: `<IFModule モジュール>...</IFModule>`  
            （指定したモジュールが適応されていた場合の設定）
            - HTTPメソッドごとの設定:
                - 指定メソッドのみに適応: `<Limit HTTPメソッド>...</Limit>`
                - 指定メソッド以外のメソッドに適応: `<LimitExcept HTTPメソッド>...</LimitExcept>`
            - バーチャルホスト設定:
                - 名前ベース: `<VirtualHost *:80>...</VirtualHost>`

    - ユーザ管理
        - Basic認証
            - `$ htpasswd [オプション] ファイル名 ユーザ名`

            | オプション | 説明 |
            | --- | --- |
            | -c | パスワードファイルの新規作成 |
            | -D | ユーザの削除 |

        - Digest認証
            - `$ htdigest [-c] ファイル名 認可領域名 ユーザ名`

    - SSL/TLS
        - サーバ秘密鍵の作成:`$ openssl genrsa -out server.key 2048`
        - 証明書発行要求書(CSR)の作成: `$ openssl req -new -key server.key -out server.csr`
        - CSRに対して認証局に署名してもらうことでサーバ証明書 `server.crt` を作成する
        - 自己署名してサーバ証明書を作成: `$ openssl req -new -x509 -key server.key -out server.crt`
        - SSL/TLS関連の主なディレクティブ

        | ディレクティブ | 説明 |
        | --- | --- |
        | SSLEngine on\|off | 有効化/無効化 |
        | SSLProtocol | 使用可能なSSLプロトコルを指定（+で有効化, -で無効化） |
        | SSLCipherSuite | 暗号スイートの指定（:で連結, !で無効化） |
        | SSLCertificateFile | サーバ証明書ファイル |
        | SSLCertificateKeyFile | サーバ秘密鍵ファイル |
        | SSLCertificateChainFile | 中間CA証明書ファイル（Apache2.4.8からはSSLCertificateFileにまとめて指定する） |
        | SSLCACertificateFile | クライアント認証に使用するCA証明書ファイル |
        | SSLCACertificatePath | クライアント認証に使用するCA証明書ファイルが置かれたディレクトリ |
        | SSLVerifyClient | クライアント認証レベル（none, optional, optional_no_ca, require） |

        - 使用可能な暗号スイートの確認: `$ openssl ciphers -v`

- Nginx
    - 設定の再読み込み: `$ nginx -s reload`
    - 終了: `$ nginx -s dtop`
    - nginx.confの主なディレクティブ

    | ディレクティブ | コンテキスト | 説明 |
    | --- | --- | --- |
    | http {} | main | httpサーバとしての設定 |
    | server {} | http | バーチャルホストの設定 |
    | location prefix 条件 {} | server, location | 条件にマッチするリクエストURIに対する設定 |
    | include | すべて | 他の設定ファイルの読み込み |
    | user | main | ワーカープロセスの実行ユーザ |
    | worker_processes | main | ワーカープロセス数（CPUコア数） |
    | worker_connections | events | 1つのワーカープロセスが同時に処理できる最大コネクション数 |
    | log_format | http | アクセスログの書式定義 |
    | access_log | http, server, location | アクセスログのパスとログレベル |
    | error_log | main, http, server, location | エラーログファイルのパスとログレベル |
    | listen | server | リクエストを受け付けるポート番号 |
    | server_name | server | サーバ名（バーチャルホスト名） |
    | keepalive_requests | http, server, location | 一度の接続で受け付ける最大リクエスト数 |
    | keepalive_timeout | http, server, location | キープアライブのタイムアウト(s) |
    | server_tokens | http, server, location | バージョン番号表示（offで非表示） |
    | root | http, server, location | ドキュメントルート |
    | index | gttp, server, location | インデックスファイル |
    | autoindex | http, server, location | インデックスリスト表示（offで非表示） |
    | error_page | http, server, location | エラーコードとエラーページURI |
    | rewrite | server, location | リダイレクト設定 |

    - SSL/TLS関連ディレクティブ
        - ssl: onで有効化
        - ssl_certificate: サーバ証明書ファイル, 中間CA証明書ファイル
        - ssl_certificate_key: サーバ秘密鍵ファイル
        - ssl_protocols: プロトコルのバージョン
        - ssl_ciphers: 利用する暗号アルゴリズム

    - リバースプロキシ関連ディレクティブ
        - proxy_pass: プロキシ先URI（locationコンテキスト）
        - proxy_http_varsion: HTTPプロトコルバージョン（1.1/2）
        - proxy_set_hrader: プロキシ先に送られるリクエストヘッダフィールドの再定義または追加
        - proxy_pass_header: プロキシ先からクライアントへの通過を許可するヘッダフィールドの指定

    - FastCGI関連ディレクティブ
        - fastcgi_pass: FastCGIサーバの指定（locationコンテキスト）
        - fastcgi_param: FastCGIサーバに渡すパラメータ設定

    - nginx.confファイルの構文チェック: `$ nginx -t`

- Squid
    - squid.confの基本設定項目
        - http_port: Squidが利用するポート番号
        - visible_hostname: ホスト名
        - hierarchy_stoplist: キャッシュを利用しない文字列
        - maximum_object_size: キャッシュ可能な最大ファイルサイズ
        - minimum_object_size: キャッシュ可能な最小ファイルサイズ
        - maximum_object_size_in_memory: メモリ上の最大ファイルサイズ
        - ipcache_size: キャッシュするIPアドレス数
        - cache_dir: キャッシュを格納するディレクトリと容量などのパラメータ
        - cache_mem: メモリ上のキャッシュサイズ
        - cache_access_log: クライアントのアクセスログ
        - cache_log: キャッシュのログ
        - ftp_user: anonymousでFTPアクセスする際のパスワード
        - ftp_passive on|off: FTPパッシブモードのオン/オフ
        - reference_age: キャッシュの保存期間
        - request_header_max_size: HTTPリクエストヘッダの最大サイズ
        - request_body_max_size: HTTPリクエストボディの最大サイズ
        - reply_body_max_size: レスポンスの最大ボディサイズ
        - acl: アクセスコントロールリストの設定
        - http_access: アクセスコントロールリストの制御
    - アクセス制御
        - アクセスコントロールリストの作成  
        `$ acl ACL名 ACLタイプ 文字列またはファイル名`

        | ACLタイプ | 説明 |
        | --- | --- |
        | src | クライアント側のIPアドレスとマスク |
        | dst | 代理アクセス先サーバのIPアドレスとマスク |
        | srcdomain | クライアントドメイン名 |
        | dstdomain | 代理アクセス先サーバドメイン名 |
        | port | 代理アクセス先サーバポート番号 |
        | myport | クライアントポート番号 |
        | arp | MACアドレス |
        | proto | プロトコル |
        | method | HTTPメソッド |
        | url_regex | URLにマッチする正規表現 |
        | urlpath_regex | URLからプロトコルとホスト名を除いたパス名にマッチする正規表現 |
        | time | 有効な時刻 |
        | proxy_auth | ユーザ認証の対象 |

        - アクセスコントロールリストに対しての制御  
        `$ http_access allow|deny ACL名`
    
    - squidclientコマンド  
    `$ squidclient オプション URL`

    | オプション | 説明 |
    | --- | --- |
    | -h ホスト | URLを検索するプロキシのホスト名/IPを指定 |
    | -p ポート番号 | プロキシのポート番号を指定（デフォルトは3128） |
    | -m メソッド | HTTPリクエストメソッドを指定 |
    | -u ユーザ名 | プロキシ認証のユーザを指定 |
    | -w パスワード | プロキシ認証のパスワードを指定 |