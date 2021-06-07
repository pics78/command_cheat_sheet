- 名前空間(namespace)
    - コンテナの隔離に使われる機能群

    | 名前空間の種別 | 隔離するリソース |
    | ---- | ---- |
    | マウント名前空間 | ファイルシステムのマウント |
    | UTS名前空間 | ホスト名やドメイン名 |
    | IPC名前空間 | IPCオブジェクトやメッセージキュー |
    | PID名前空間 | プロセスID |
    | ネットワーク名前空間 | 各種ネットワークリソース |
    | ユーザ名前空間 | UIDとGID |

    - 新しい名前空間を作成してプロセスを実行する: `$ unshare`
- cgroup(Control Group)
    - プロセスをグループ化して管理する仕組み
    - グループごとにシステムリソースの利用に制限をかけることができる
    - 管理できるシステムリソースの確認: `$ ls /sys/fs/cgroup/`
- Docker
    - Dockerイメージ: コンテナの雛形となるコンテナイメージ
    - Dockerレジストリ: Dockerイメージを管理するサイト
    - Dockerエンジン: コンテナ化する仕組み
    - Dockerサービスの起動: `$ systemctl start docker.service`
    - Dockerのバージョン確認: `$ docker version`
    - 主なdockerコマンド

    | コマンド | 説明 |
    | ---- | ---- |
    | docker ps | コンテナ一覧を表示 |
    | docker stat | コンテナの詳細を表示 |
    | docker run | コンテナの起動 |
    | docker create | Dockerイメージからコンテナを生成 |
    | docker attach | コンテナに接続 |
    | docker restart | コンテナを再起動 |
    | docker pause | コンテナのプロセスを一時停止 |
    | docker unpause | 一時停止したコンテナのプロセスを再開 |
    | docker stop | コンテナを停止 |
    | docker kill | コンテナを強制的に停止 |
    | docker rm | コンテナを削除 |
    | docker rmi | Dockerイメージを削除 |
    | docker images | Dockerイメージの一覧うぃ表示 |
    | docker pull | Dockerイメージを取得 |
    | docker import | tarアーカイブからDockerイメージを作成 |
    | docker build | 新しいDockerイメージを作成 |
    | docker commit | コンテナの変更をもとに新しいDockerイメージを作成 |
    | docker network create | Dockerネットワークを作成 |
    | docker network connect | Dockerネットワークに接続 |

    - 一般ユーザでもdockerコマンドを使えるようにする
        - 対象ユーザをdockerグループに所属させる  
        `$ groupadd docker`  
        `$ usermod -aG docker 対象ユーザ`
    - 基本操作
        - Dockerレジストリからコンテナイメージを取得: `$ docker pull リポジトリ名[:タグ名]`
            - 例.CentOSのDockerイメージを取得: `$ docker pull centos:8`
        - 取得したコンテナイメージの一覧: `$ docker images`