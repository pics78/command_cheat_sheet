- UEFIにおける起動エントリを管理する
    - 起動エントリ一覧  
        `$ efibootmgr -v`  
        -v, --verbose: 起動エントリ一覧の詳細表示  
    - 起動エントリ作成例  
        `$ efibootmgr -c -d /dev/sda -p 1 -l "EFI/centos/shimx64.efi" -L CentOS7`  
        -c, --create: 新しい起動エントリ作成  
        -d, --disk: ディスク指定  
        -p, --part: パーティション番号指定  
        -l, --loder: ブートローダ指定  
        -L, --label: ラベル指定
- カーネル起動中に表示されるメッセージの確認
    - `$ journalctl -b`
    - `$ dmesg`
- 初期RAMディスクの作成
    - `$ mkinitrd`
    - `$ mkinitramfs -o`
    - `$ dracut "initramfs-$(uname -r).img" $(uname -r)`  
    (現在のカーネルバージョンに合わせたinitramfsファイルを作成)
- GRUBのインストール
    - `$ grub-install`
    - `$ grub2-install`
- GRUBの設定(/etc/default/grubファイルからgrub.cfgを生成)
    - `$ grub-mkconfig`
    - `$ grub2-mkconfig`
- 起動オプションの確認
    - `$ cat /proc/cmdline`
- systemd
    - デフォルトターゲットの設定(グラフィカルターゲット)  
        - `$ ln -sf /lib/systemd/system/graphical.target /etc/systemd/system/default.target`  
        - `$ systemctl set-default graphical.target`
    - サービス操作
        - 起動: `$ systemctl start`
        - 終了: `$ systemctl stop`
        - 再起動: `$ systemctl restart`
        - 稼働状況表示: `$ systemctl status`
        - 稼働確認: `$ systemctl is-active`
        - 自動起動OFF: `$ systemctl disable`
        - 自動起動ON: `$ systemctl enable`
        - 稼働中のサービス一覧: `$ systemctl list-units --type=service`
        - 全サービス一覧: `$ systemctl list-unit-files --type=service`
    - メンテナンスモード
        - レスキューモード(シングルユーザモード)へ切り替え
            - `$ systemctl isolate rescue.target`
            - `$ systemctl rescue`
        - 緊急モードへ切り替え
            - `$ systemctl isolate emergency.target`
            - `$ systemctl emergency`
- カーネルバージョンの確認
    - `$ uname -r`
    - `$ cat /proc/varsion`
    - カーネルソースからコンパイルしている場合はそのMakefileの先頭を参照することでも確認可
- カーネルイメージの確認
    - `$ ls /boot/vmlinuz*`  
    (イメージファイル名は一般に「vmlinuz-カーネルバージョン」という形式になっている)
- カーネルモジュール
    - ロード中のモジュール一覧
        - `$ lsmod`
        - `$ cat /proc/modules`
    - モジュールの情報表示
        - `$ modinfo`
    - モジュールのロード
        - `$ insmod` (依存関係は手動解決)
        - `$ modprobe` (依存関係は自動解決)  
        ＊依存関係はmodules.depファイルに記述されている
    - モジュールのアンロード
        - `$ rmmod` (依存関係は手動解決)
        - `$ modprobe -r` (依存関係は自動解決)  
        ＊依存関係はmodules.depファイルに記述されている
- カーネルのコンパイル
    - 主な手順  
    ```
        1. カーネルソースの用意
        2. カーネルコンフィギュレーションの設定
        3. カーネルのコンパイル
        4. カーネルモジュールのコンパイル
        5. カーネルとカーネルモジュールの配置
        6. ブートローダの設定変更
    ```
    - カーネルソースの用意  
    `https://www.kernel.org/`から入手  
    `/usr/src`下に配置し展開する  
    `$ xz -dc linux-(バージョン).tar.xz | tar xvf -`
    - カーネルコンフィギュレーションの設定  
    (設定は`.config`ファイルに記録される)
        - 現在の設定を新しい設定にも反映させたい場合: `$ make oldconfig`
        - コンソール上でオプションごとに質問に答える形で順に設定: `$ make config`
        - コンソール上でメニュー形式で表示されているオプション項目を選択して設定: `$ make menuconfig`
        - X(KDE)上で設定: `$ make xconfig`
        - X(GNOME)上で設定: `$ make gconfig`
    - カーネルとカーネルモジュールのコンパイル
        - 両方を一括コンパイル: `$ make`
    - カーネルモジュールとカーネルのインストール
        - カーネルモジュールのインストール: `$ make modules_install`  
        (インストール先は`/lib/modules/カーネルバージョン)
        - カーネルのインストール: `$ make install`  
        処理内容:
            - カーネルを/boot以下にコピー
            - 初期RAMディスクが必要な場合は作成
            - ブートローダの設定ファイルに新しいカーネル用のエントリを追加
        - GRUBに登録されているエントリの確認: `$ grep "^menuentry" /boot/grub2/grub.cfg | cut -d "'" -f 2`
        - デフォルトカーネルを上記のコマンド結果の0番目(一番上)のものに変更: `$ grub2-set-default 0`
        - 設定ファイルの更新: `$ grub2-mkconfig -o /boot/grub2/grub.cfg`
    - DKMS(Dynamic Kernel Module Support)
        - ソースツリー外にあるカーネルモジュールを自動的に生成する
        - カーネルをアップデートし再起動するとサードパーティ製カーネルモジュールを自動的にリビルドする
        - 手動ビルドもできる
            - (例)カーネルバージョン4.19.132用にNVIDIAビデオドライバをビルドする  
            `$ dkms install nvidia/450.57 -k 4.19.132`
- カーネルパラメータの変更
    - 主なパラメータ(/proc/sys以下)  
    ```
        - kernel/ctrl-alt-del                   : [Ctrl]-[Alt]-[Delete]キーの動作設定
        - kernel/hostname                       : ホスト名
        - kernel/modprobe                       : modprobeのパス
        - kernel/hotplug                        : ホットプラグ用プログラムのパス
        - kernel/osrelease                      : カーネルバージョン
        - kernel/ostype                         : OSの種類
        - kernel/sem                            : セマフォ数
        - kernel/shmall                         : 共有メモリの合計(バイト単位)
        - kernel/shmmax                         : 共有メモリセグメントの最大値(バイト単位)
        - kernel/shmmni                         : 共有メモリセグメントの最大数
        - kernel/sysrq                          : システム要求キー(MagicSysRqKey)の有効/無効設定
        - kernel/threads-max                    : スレッド(プロセス)最大数
        - kernel/version                        : カーネル構築の情報
        - fs/file-max                           : ファイルハンドルの最大数
        - fs/file-nr                            : 開かれているファイル数, 使用されたファイルハンドル数, ファイルハンドルの最大数
        - net/core/rmem_default                 : 受信ソケットバッファのデフォルトサイズ
        - net/core/rmem_max                     : 受信ソケットバッファの最大サイズ
        - net/core/wmem_default                 : 送信ソケットバッファのデフォルトサイズ
        - net/core/wmem_max                     : 送信ソケットバッファの最大サイズ
        - net/ipv4/ip_forward                   : ネットワークインターフェース間でのパケット転送の有効/無効設定
        - net/ipv4/icmp_echo_ignore_broadcast   : ブロードキャスト宛のICMPEchoRequestを無視するかどうか
        - net/ipv4/icmp_echo_ignore_all         : すべてのICMPEchoRequestを無視するかどうか
    ```
    - パラメータ一覧: `$ sysctl -a`
    - 設定変更: `$ sysctl -w net.ipv4.ip_forward=1`(-wオプションは省略可)
    - 変更した設定を再起動後も有効にするには`/etc/sysctl.conf`に追記する必要がある
- カーネル管理
    - PCIバスとデバイスの情報表示: `$ lspci`
    - USBデバイスの情報表示: `$ lsusb`
    - カーネルが認識しているデバイスの一覧表示: `$ cat /proc/devices`
- udev
    - sysfsと連動して動的にデバイスファイルを管理する
    - カーネルがデバイスを検知するとudevdデーモンが必要なデバイスファイルを動的に作成する
    - 設定ファイルは`/etc/udev/rules.d/`以下に配置
    - sysfsがどのようにデバイスを扱っているかを確認: `$ udevadm info`
        - `$ udevadm info -q path -n /dev/sda`
        - (より詳細に)`$ udevadm info -q env -n /dev/sda`
    - デバイスの検知をモニタリングする: `$ udevadm monitor`