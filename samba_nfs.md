- Sambaサーバ
    - プロセス
        - smbd: ファイル共有, 認証など
        - nmbd: ブラウジング機能, NetBIOS名前解決, WINSサーバなど
        - winbindd: Winbind機能
    - 利用ポート
        - 137/UDP: NetBIOS名前解決, ブラウジングなど
        - 138/UDP: NetBIOS名前解決, ドメインログオン
        - 139/TCP: ファイル共有
        - 445/TCP: ファイル共有（Microsoft Direct Hosting SMB利用時）
    - 起動
        - `$ systemctl start smb.service`
        - `$ systemctl start nmb.service`
    - smb.conf設定
        - globalセクション
            - workgroup = ワークグループ名|ドメイン名  
            - server role = 動作モード

            | 動作モード | 説明 |
            | --- | --- |
            | AUTO | securityパラメータの設定に従う |
            | STANDALONE | スタンドアロンサーバ |
            | MEMBER SERVER | ドメインのメンバーサーバ |
            | CLASSIC PRIMARY DOMAIN CONTROLLER | NTドメインのプライマリドメインコントローラ（PDC） |
            | CLASSIC BACKUP DOMAIN CONTROLLER | NTドメインのバックアップドメインコントローラ（BDC） |
            | ACTIVE DIRECTORY DOMAIN CONTROLLER | ActiveDirectoryのドメインコントローラ |

            - netbios name = NetBIOS名
            - server string = コメント
            - hosts allow = ホスト
            - guest account = ゲストユーザ名
            - map to guest = Never|Bad User|Bad Password
            - log file = ログファイル名
            - max log size = サイズ
            - encrypt passwords = Yes|No
            - smb passwd file = パスワードファイルのパス
            - unix password sync = Yes|No
            - passwd program = パスワードコマンドのパス
            - passwd chat = 期待される文字列
            - username map = マッピングファイル名
            - logon script = スクリプトファイル名
            - wins support = Yes|No
            - wins server = IPアドレス

        - 共通設定項目
            - comment = コメント
            - browseable = Yes|No
            - writable = Yes|No
            - read only = No|Yes
            - path = 共有ディレクトリのパス
            - force user = ユーザ名
            - force group = グループ名
            - write list = ユーザ名または@グループ名
            通常書き込み禁止の場合に例外的に書き込みが許可されるユーザやグループ
            - hide dot files = Yes|No
            - hide files = /ファイル名/
            （表示させたくないファイル）
            - veto files = /ファイル名/
            （表示もアクセスもさせたくないファイル）
            - create mask = mode（デフォルト: 0744）
            - directory mask = mode（デフォルト: 0755）
            - force create mode = mode（デフォルト: 0000）
            - force directory mode = mode（デフォルト: 0000）
            - valid users = ユーザ名
            - guest ok = Yes|No
            - public = Yes|No

        - smb.confの構文チェック: `$ testparm [オプション] [設定ファイル]`

        | オプション | 説明 |
        | --- | --- |
        | -s, --suppress-prompt | 構文チェック後に[Enter]キーを押さずともsmb.confを表示 |
        | -v, --verbose | デフォルト値のパラメータも表示 |
        | --show-all-parameters | すべてのパラメータを表示 |
    
    - Sambaユーザ管理
        - pdbeditコマンド  
        `$ pdbedit [オプション] Sambaユーザ名`

        | オプション | 説明 |
        | --- | --- |
        | -L | 一覧表示 |
        | -a | ユーザの追加 |
        | -x | ユーザの削除 |

        - smbpasswdコマンド  
        `$ smbpasswd [オプション] [Sambaユーザ名]`
            - パスワード変更: `$ smbpasswd Sambaユーザ名`

        | オプション | 説明 |
        | --- | --- |
        | -d | ユーザの無効化 |
        | -e | ユーザの有効化 |
        | -x | ユーザの削除 |
    
    - Samba管理
        - smbcontrolコマンド  
        `$ smbcontrol 対象 メッセージタイプ`

        | 対象 | 説明 |
        | --- | --- |
        | all | smbd,nmbd,winbinddのすべてのプロセスにブロードキャスト |
        | デーモン名またはPID | 指定した対象にメッセージが送られる |

        | メッセージタイプ | 説明 |
        | --- | --- |
        | close-share | 指定した共有をクローズ（smbdのみ） |
        | reload-config | 指定デーモンの設定を再読み込み |
        | kill-client-ip | 指定したIPアドレスのクライアントを切断（smbdのみ） |
        | ping | 指定対象にpingし応答が来た対象のPIDを表示 |

        - smbstatusコマンド

        - nmblookupコマンド  
        `$ nmblookup [オプション] NetBIOS名またはIPアドレス,ワークグループ名`

        | オプション | 説明 |
        | --- | --- |
        | -A | 引数をIPアドレスとみなす |

    - Sambaクライアント
        - smbclientコマンド  
        `$ smbclient [オプション] 接続先`

        | オプション | 説明 |
        | --- | --- |
        | -L | リスト表示 |
        | -N | 認証を行わない |
        | -U ユーザ | 接続するユーザを指定 |
        
        | サブコマンド | 説明 |
        | --- | --- |
        | cd ディレクトリ | ディレクトリの移動 |
        | lcd ディレクトリ | ローカル側のディレクトリを移動 |
        | del ファイル | ファイルの削除 |
        | rmdir ディレクトリ | ディレクトリの削除 |
        | dir | ファイルリストの表示 |
        | exit | smbclientを終了する |
        | get ファイル | ファイルを取得 |
        | mget ファイル | 複数ファイルをまとめて取得 |
        | mkdir ディレクトリ | ディレクトリを作成 |
        | put ファイル | ファイルを転送 |
        | mput ファイル | 複数ファイルをまとめて転送 |

    - ACL
        - ACLエントリ表示: `$ getfacl ファイル`

        | No | ACLエントリ | 説明 |
        | --- | --- | --- |
        | 0 | default: エントリ | デフォルトの許可 |
        | 1 | user::rwx | 所有者のアクセス権 |
        | 2 | group::rwx | 所有グループのアクセス権 |
        | 3 | other::rwx | その他のユーザのアクセス権 |
        | 4 | user:USER:rwx | ユーザUSERのアクセス権 |
        | 5 | group:GROUP:rwx | グループGROUPのアクセス権 |
        | 6 | mask::rwx | マスク値（2, 4, 5に適用） |

        - ACL設定: `$ setfacl オプション エントリ対象ファイル/ディレクトリ`

        | オプション | 説明 |
        | --- | --- |
        | -m エントリ | 指定エントリの追加・変更 |
        | -x エントリ | 指定エントリの削除 |
        | -b | 基本エントリ以外のすべてのエントリを削除 |
        | -d | 指定エントリをデフォルトのACLに変更 |
