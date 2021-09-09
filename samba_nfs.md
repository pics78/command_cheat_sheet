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