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
        - コンテナの生成: `$ docker create [--name コンテナ名] リポジトリ名[:タグ名]`
            - 例.CentOSのDockerイメージからmycentosという名前でコンテナを生成: `$ docker create --name mycentos centos:8`
        - コンテナの起動: `$ docker start コンテナ名orコンテナID`
            - 例.mycentosを起動: `$ docker start mycentos`  
            -> 何も指定しないと一瞬で終了する
        - docker run
            - `$ docker run リポジトリ名[:タグ名] コマンド`
            - docker pull/create/startを合わせた動きをする
            - 引数のコマンドにはコンテナ内で実行させるコマンドを指定する
                - 例.CentOSのコンテナ内でCentOSのバージョンを確認する: `docker run centos:8 cat /etc/redhat-release`
            - 終了したコンテナも含めて表示: `$ docker ps -a`
            - コンテナの削除: `$ docker rm コンテナID`  
            (コンテナIDは最初の4文字のみの指定でも大丈夫)
            - コンテナへのログイン: `$ docker run -it --name コンテナ名 イメージ名[:タグ名] シェル`  
            ＊ -i(--interactive): コンテナの標準入力に接続  
            ＊ -t(--tty): 疑似端末を割り当てる
                - 例.CentOSのDockerイメージをmycentosとして起動しbashシェルにログイン: `$ docker run -it --name mycentos centos:8 /bin/bash`  
                -> プロンプトが変わりrootユーザでログインした状態になる
        - コンテナから一時的に抜けた後にもう一度接続する: `$ docker attach コンテナ名orコンテナID`
        - コンテナから抜けて終了させる: `$ [root@......... /]# exit` (コンテナ内シェル)
    - 仮想ネットワーク
        - コンテナはDockerが作り出す仮想ネットワークを利用している
            1. コンテナを起動してbashシェルを実行: `$ docker run -itd --name mycentos centos:8 /bin/bash`  
            ＊ -d: コンテナに接続しない(detouch)
            2. コンテナ内のシェルでipコマンドを実行: `$ docker exec mycentos /sbin/ip a`  
            -> IPアドレスが割り当てられていることを確認できる
            3. ホストOS側には仮想ネットワークインターフェースが作成されている: `$ ip a show docker0`
        - 新しい仮想ネットワークインターフェースを作成: `$ docker network create 仮想ネットワーク名`
        - 作成したネットワークにコンテナを接続: `$ docker network connect 仮想ネットワーク名 コンテナ名orコンテナID`
    - Dockerイメージの作成
        - コンテナからDockerイメージを作成: `$ docker commit コンテナID イメージ名[:タグ名]`
            - 「test/centos」という名前でDockerイメージを作成: `$ docker commit e41e test/centos`
        - 不要になったDoclkerイメージの削除: `$ docker rmi イメージ名orコンテナID
            - Dockerイメージtest/centosを削除: `$ docker rmi test/centos`
    - ポート変換
        - -pオプションでホストOSにポートとコンテナのポートを変換することができる
        - Nginx用コンテナをバックグラウンドで起動しホストOSの8080番ポート経由でコンテナの80b番ポートにアクセスできるようにする  
        `$ docker run --name nginxtest -d -p 8080:80 nginx`  
        ＊ -d: バックグラウンドで起動
        - コンテナ外に作成したファイルをコンテナ内から参照するには-vオプションを使う
            - 例: `$ docker run --name nginxtest2 -v /home/centuser/html:/usr/share/nginx/html -d -p 8080:80 nginx`
    - Dockerfile
        - 既存のDOckerイメージに操作を加えて新しいDockerイメージを作成できる
        - Dockerイメージを作成する際のMakefileに相当する
        - Dockerfileの設定項目

        | 項目 | 説明 |
        | ---- | ---- |
        | FROM | 元になるDockerイメージ |
        | MAINTAINER | 管理者 |
        | RUN | 指定したコマンドを実行 |
        | CMD | 指定したコマンドおよび引数を実行 |
        | ADD | 指定したファイルやディレクトリを追加 |
        | EXPOSE | 指定したポートを開く |
        | USER | 実行ユーザを指定 |

        - DockerfileからDockerイメージを作成: `$ docker build -t リポジトリ名:タグ名 Dockerファイルのあるディレクトリ`
    - コンテナの運用・管理
        - コンテナの状態を詳しく確認: `$ docker stats`
        - 圧縮アーカイブファイルからDockerイメージを生成: `$ docker import ファイル名orURL [リポジトリ名[:タグ名]]`
        - コンテナ内のすべてのプロセスを一時停止: `$ docker pause コンテナ`
        - 一時停止したコンテナを再開: `$ docker unpause コンテナ`
        - コンテナを停止、終了: `$ docker stop コンテナ`
            - 終了できない場合はコンテナ内のメインプロセスに対してKILLシグナルを送信 `$ docker kill [-s シグナル] コンテナ`
    - プライベートレジストリの構築
        - レジストリ用のDockerイメージが用意されているため5000番ポートを開き稼働する: `$ docker run -d -p 5000:5000 --name registry registry:2`
        - レジストリへの登録: `$ docker push`
            1. Dockerイメージubuntuを取得: `$ docker pull ubuntu`
            2. 取得したイメージにタグ付け: `$ docker tag ubuntu:latest localhost:5000/ubuntu`
            3. レジストリに登録: `$ docker push localhost:5000/ubuntu` 