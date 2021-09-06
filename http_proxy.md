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
        - apachectlコマンド
            - 書式: `$ apachectl サブコマンド`

            | サブコマンド | 説明 |
            | --- | --- |
            | start | 起動 |
            | stop | 終了 |
            | restart | 再起動 |
            | graceful | 安全に再起動（リクエスト処理中の場合は完了を待つ） |
            | reload | 設定ファイルの再読み込み |
            | configtest | 設定ファイルの構文チェック |
    
    - httpd.confの主な設定値
        - ServerTokens Prod|Major|Minor|Min|OS|Full  
        右にいくほど詳細なバージョン情報を公開（通常はProd）
        - ServerRoot パス  
        httpdの利用するトップディレクトリ
        - ServerName サーバのホスト名
        - ServerAdmin 管理者のメールアドレス  
        エラーページなどに表示されるようになる
        - StartServers 起動時の子プロセス数
        - MinSpareServers 待機子プロセス数の最小値
        - MaxSpareServers 待機子プロセス数の最大値
        - ServerLimits 生成子プロセスの設定可能上限
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
        - Redirect アクセスディレクトリまたはファイル 転送先URL
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