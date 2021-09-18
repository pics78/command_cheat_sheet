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
            - hosts deny = ホスト
            - guest account = ゲストユーザ名
            - map to guest = Never|Bad User|Bad Password
            - log file = ログファイル名
            - max log size = サイズ
            - encrypt passwords = Yes|No
            - smb passwd file = パスワードファイルのパス
            - unix password sync = Yes|No
            - passwd program = パスワードコマンドのパス
            - passwd chat = 期待される文字列
            - null passwords = Yes|No
            - username map = マッピングファイル名
            - logon script = スクリプトファイル名
            - wins support = Yes|No
            - wins server = IPアドレス
            - security = 認証方法

            | 認証方法 | 説明 |
            | --- | --- |
            | user | ユーザ名とパスワードでローカル認証を行う |
            | share | パスワードだけで認証を行う |
            | domain | ドメインコントローラにより認証を行う |
            | server | 他のSMBサーバにより認証を行う |
            | ads | ADドメインのドメインコントローラで認証を行う |

            - passdb backend = パスワードデータベース

            | パスワードデータベース | 説明 |
            | --- | --- |
            | tdbsam | TDB（TrivalDataBase） |
            | ldapsam | LDAP |
            | smbpasswd | smbpasswdファイル |

            - Winbind関連

            | 項目 | 説明 |
            | --- | --- |
            | idmap config * : backend = バックエンド | idmap機構で使用するバックエンドの指定（tdb, ldapなど） |
            | idmap config * : range = 最小値-最大値 | Linuxユーザに割り当てるUID,GIDの範囲を指定 |

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
  
        - printersセクション
            - print ok = Yes|No
            - printable = Yes|No

        - smb.confの構文チェック: `$ testparm [オプション] [設定ファイル]`

        | オプション | 説明 |
        | --- | --- |
        | -s, --suppress-prompt | 構文チェック後に[Enter]キーを押さずともsmb.confを表示 |
        | -v, --verbose | デフォルト値のパラメータも表示 |
        | --show-all-parameters | すべてのパラメータを表示 |

        - Samba変数
            - %v: Sambaのバージョン番号
            - %U: セッションのユーザ名
    
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
        | -a | ユーザの追加 |
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
        Sambaサーバに接続しているクライアント等、稼働状況の確認を行う

        - nmblookupコマンド  
        `$ nmblookup [オプション] NetBIOS名|IPアドレス|ワークグループ名`

        | オプション | 説明 |
        | --- | --- |
        | -M | マスターブラウザを検索 |
        | -A | 指定されたIPアドレスのMACアドレスやNetBIOS名などを表示 |

    - Sambaクライアント
        - smbclientコマンド  
        `$ smbclient [オプション] 接続先`

        | オプション | 説明 |
        | --- | --- |
        | -L ホスト名, --list=ホスト名 | リスト表示 |
        | -U ユーザ名, --user=ユーザ名 | 接続するユーザを指定 |
        | -N | 認証を行わない |
        
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
        | --remove-all | すべてのACLエントリを削除 |

        - netコマンド

        | コマンド例 | 説明 |
        | --- | --- |
        | net lookup host01 | host01のIPアドレスを表示 |
        | net lookup dc sample | sampleドメインのドメインコントローラのIPアドレスを表示 |
        | net lookup master | マスターブラウザのIPアドレスを表示 |
        | net domain | ワークグループ一覧を表示 |
        | net status sessions | セッション一覧を表示 |
        | net status shares | 共有ごとのアクセス一覧を表示 |
        | net ads join -U ユーザ名 | ActiveDirectoryドメインに参加 |

- NFSサーバ
    - RPC（Remote Procedure Call）  
    ネットワーク上にあるリモートホストの機能を別のホストから使えるようにする仕組み
        - RPCプログラム番号とポート番号の対応: `/etc/rpc`
        - RPCプログラム番号とポート番号のマッピングデーモン: portmap（portmapper）
        - RPCサービスの状況: rpcinfoコマンド  
        `$ rpcinfo オプション [ホスト名]`

        | オプション | 説明 |
        | --- | --- |
        | -p | 稼働しているRPCサービスの一覧を表示 |
    
    - NFSサーバの設定
        - サービスの開始
            - `$ systemctl start rpcbind`
            - `$ systemctl start nfs-server`
            - `$ systemctl start nfs-lock`
        - エクスポートするディレクトリの設定: `/etc/exports`  
        書式: `公開するディレクトリ 公開するクライアント(オプション)`

        | 主なオプション | 説明 |
        | --- | --- |
        | ro | 読み取り専用（デフォルト） |
        | rw | 読み書き可 | 
        | no_root_squash | rootでのアクセス時にroot権限で実行 |
        | root_squash | rootでのアクセス時に匿名アカウントで実行（デフォルト） |
        | all_squash | すべてのアクセスを匿名アカウント権限で実行 |

        - exportfsコマンド  
        現在のエクスポート状況の確認, /etc/exportsの変更反映  
        `$ exportfs [オプション] [ホスト:パス ...]`

        | オプション | 説明 |
        | --- | --- |
        | -a | すべてのディレクトリをエクスポート（アンエクスポート） |
        | -r | すべてのディレクトリを再エクスポート |
        | -u | アンエクスポート |
        | -v | 詳細に表示 |

        - showmountコマンド  
        NFSサーバでエクスポートしているディレクトリを調べる  
        `$ showmount [オプション] [ホスト]`

        | オプション | 説明 |
        | --- | --- |
        | -a | クライアントのホスト名とマウントしているディレクトリを表示 |
        | -e | 指定したホストでエクスポートしているディレクトリを表示 |

        - nfsstatコマンド  
        NFSの統計情報表示  
        `$ nfsstat [オプション]`

        | オプション | 説明 |
        | --- | --- |
        | なし | NFSサーバ, NFSクライアント, RPCの統計を表示 |
        | -s | NFSサーバ側の統計のみ表示 |
        | -c | NFSクライアント側の統計のみ表示 |
        | -n | NFSの統計のみ表示 |
        | -r | RPCの統計のみ表示 |
        | -o net | ネットワーク層の統計を表示 |

    - NFSクライアントの設定
        - mountコマンドをファイルシステムタイプにnfsを指定して実行  
        `$ mount -t nfs -o マウントオプション NFSサーバ名:エクスポートディレクトリ マウントポイント`

        | NFS特有のマウントオプション | 説明 |
        | --- | --- |
        | bg | マウントに失敗してもバックグラウンドで試行を続ける |
        | fg | マウントはフォアグラウンドで実行 |
        | sort | ソフトマウント |
        | hard | ハードマウント |
        | intr | ハードマウント時の割り込みを許可 |
        | retrans | ソフトマウントの場合の再試行回数 |
        | nolock | ファイルのロックを行わない |
        | rsize=バイト数 | 読み取りのブロックサイズ |
        | wsize=バイト数 | 書き込みのブロックサイズ |