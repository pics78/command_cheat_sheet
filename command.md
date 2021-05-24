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
    - `journalctl -b`
    - `dmesg`

