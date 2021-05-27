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
