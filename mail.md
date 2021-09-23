- Postfix
    - /etc/postfix/main.cfの設定値
        - myhostname: 自ホスト名
        - mydomain: ドメイン名
        - myorigin: メールアドレスに適用されるドメイン名
        - inet_interfaces: SMTP接続を待ち受けるネットワークインターフェース
        - inet_protocols: IPv4/IPv6の動作をall/ipv4/ipv6で指定
        - mydestination: ローカル配送を行うドメイン名（メールを受け取るドメイン名)
        - mynetworks: 中継を許可するSMTPクライアントのアドレス
        - mail_spool_directory: メールスプールディレクトリ
        - home_mailbox: ユーザのホーム配下のメールスプールディレクトリを指定（相対パス）
        - mailbox_command: ローカル配送を行うプログラム
        - disable_vrfy_command: SMTPコマンドのVRFYの使用の無効化（yes）有効化（no）を指定
        - relay_domains: 許可された送信先以外からのメールの中継先を指定（デフォルトは$mydestination）
        - relayhost: 配送方法（配送先）を指定
        - transport_maps: メールリレーの設定情報を指定
        - sender_canonical_maps: 送信者アドレスの書き換え時に参照する検索テーブルを指定
        - smtpd_banner: SMTPで出力されるバナー情報
        - smtpd_use_tls: yesでTLSの有効化（2.3より前）
        - smtpd_enforce_tls: yesでTLSを強制（2.3より前）
        - smtpd_tls_security_level: TLS適用レベル（2.3以降）

        | レベル | 説明 |
        | --- | --- |
        | may | TLSを有効化, 適用は任意（クライアントに依存） |
        | encrypt | TLSを有効化, 強制適用 |

        - smtpd_tls_cert_file: サーバ証明書ファイルを指定
        - smtpd_tls_CAfile: CAの証明書ファイルを指定
        - smtpd_tls_key_file: サーバ秘密鍵ファイルを指定
        - smtpd_sasl_auth_enable: yesでSASL認証の有効化, noで無効化
        - smtpd_sasl_type: 認証に使うSASLプラグインの種類を指定
        - smtpd_sasl_path: PostfixとSASLプラグインが認証情報を受け渡す方法を指定
        - smtpd_sasl_local_domain: SASL認証のレルム（ドメイン）を指定
        - 

    - /etc/postfix/master.cfの設定値
        - smtps: コメントを外すとTLS設定を有効化

    - postconfコマンド
        - `$ postconf`: Postfixの全設定値を表示
        - `$ postconf -n`: デフォルト値から変更されている項目のみを表示
        - `$ postconf パラメータ`: 指定したパラメータのみを表示
    - postfixコマンド
        - サブコマンド一覧

        | サブコマンド | 説明 |
        | --- | --- |
        | check | 設定ファイルの構文チェック |
        | start | 開始 |
        | stop | 停止 |
        | abort | 強制停止 |
        | reload | 設定の再読み込み |
        | flush | キュー内にあるメッセージの再送 |

    - 接続確認
        - SMTPの主なメソッド

        | メソッド | 説明 |
        | --- | --- |
        | HELO ホスト | SMTPセッションの開始 |
        | EHLO ホスト | 拡張HELO |
        | MAIL FROM: | メール送信元の指定 |
        | RCPT TO: | メールの宛先を指定 |
        | DATA | メール本文を開始 |
        | QUIT | SMTPセッションの終了 |
        | VRFY ユーザ | ユーザの確認 |
        | EXPN ユーザ | ユーザのエイリアス確認 |

        - ローカルホストの25番ポート（SMTP）にアクセス: `$ nc localhost 25`  
        -> 成功コード 220
        - ホスト名の入力: `EHLO example.com`  
        -> 成功コード 250
    
    - エイリアス設定
        - /etc/aliasesで指定できる別名

        | 別名 | 説明 |
        | --- | --- |
        | /path | 指定したパスにあるファイルにメールのメッセージを追加 |
        | \|command | 指定したコマンドの標準入力にメールのメッセージを送る |
        | user@domain | 指定したメールアドレスにメールを転送 |
        | :include:/path | パスに指定したファイルを別名として読み込む |

    - /etc/aliasesファイルの変更反映: `$ newaliases`
    - SMTPサーバの運用と管理
        - Postfixの主なメールキュー

        | メールキュー | 説明 |
        | --- | --- |
        | maildrop | ローカル配信に使用される |
        | incoming | 送受信処理中のメール保持 |
        | active | 配信待ちメール保持 |
        | deferred | 送信に失敗したメール保持 |

        - メールキューの表示
            - `$ mailq`
            - `$ postqueue -p`
        - キュー内のメールの再送
            - `$ postqueue -f`
            - `$ postfix flush`
        - キュー内のメール削除
            - `$ postsuper -d キューID`: 指定したIDのメールを削除
            - `$ postsuper -d ALL`: キュー内のすべてのメールを削除
- Dovecot
    - 主な設定ファイル一覧

    | 設定ファイル | 説明 |
    | --- | --- |
    | /etc/dovecot/dovecot.conf | メイン設定ファイル |
    | /etc/dovecot/conf.d/10-auth.conf | ユーザ認証関連 |
    | /etc/dovecot/conf.d/10-logging.conf | ログ関連 |
    | /etc/dovecot/conf.d/10-mail.conf | メール配送関連 |
    | /etc/dovecot/conf.d/10-master.conf | 基本動作関連 |
    | /etc/dovecot/conf.d/10-ssl.conf | SSL/TLS関連 |
    | /etc/dovecot/conf.d/15-lda.conf | ローカル配送関連 |
    | /etc/dovecot/conf.d/20-imap.conf | IMAP関連 |
    | /etc/dovecot/conf.d/20-pop3.conf | POP3関連 |

    - dovecot.confの設定項目
        - listen = IPアドレス（*ですべてのアドレス, IPv6では::）
        - protocols = プロトコル（例: imap pop3 lmtp）
        - verbose_proctitle = yes|no  
        yesでpsコマンドの出力に詳細（ユーザ名, IPアドレス）を表示
    - 10-mail.confの設定項目
        - mail_location = メールボックス
            - Maildir形式: maildir:~/Maildir
            - mbox形式: mbox:~/mail:INBOX=/var/spool/mail/%u
    - 10-auth.confの設定項目
        - auth_mechanisms = 認証方式

        | 値 | 説明 |
        | --- | --- |
        | plain | 平文によるユーザ認証（RFC4616） |
        | login | 平文によるユーザ認証（標準仕様なし） |
        | cram-md5 | チャレンジレスポンス方式によるユーザ認証（RFC2195） |
    - 20-imap.confの設定項目
        - mail_max_userip_connections = 同一IPとユーザから接続できる最大数
    - 10-ssl.confの設定項目
        - POP/IMAP over SSLの有効化
            - ssl = yes
            - ssl_cert = <サーバ証明書ファイルのパス
            - ssl_key = <サーバ鍵ファイルのパス

    - メール受信関連の主なポート番号

    | ポート番号 | プロトコル |
    | --- | --- |
    | 110 | POP3 |
    | 143 | IMAP |
    | 993 | IMAP over SSL/TLS（IMAP3） |
    | 995 | POP3 over SSL/TLS（POP3S） |

    - （例）POPでの接続
        - `$ nc localhost 110`: localhostに接続
        - `USER ユーザ名`: ユーザ名を指定
        - `PASS パスワード`: パスワードを指定
        - `LIST`: メールの一覧を取得
        - `QUIT`: 接続終了
    - Dovecotの設定内容表示
        - `$ doveconf`: デフォルト値も含めすべて表示
        - `$ doveconf -n`: デフォルト値以外を表示
        - `$ doveconf -c` : 設定ファイルを指定（デフォルトは/etc/dovecot/dovecot.conf）
    - doveadmコマンド
        - 主なサブコマンド

        | サブコマンド | 説明 |
        | --- | --- |
        | reload | 設定の再読み込み |
        | stop | dovecotプロセスの停止 |
        | log find | ログファイルのパスを確認 |
        | log test | テストログメッセージを生成 |
        | pw | パスワードのハッシュ値を生成（-sで形式を指定） |
        | config | 設定内容の表示 |
        | mailbox | メールボックス管理 |
        | who | サーバにログイン中のユーザ情報表示（ユーザ名, pid, IPアドレスなど） |
