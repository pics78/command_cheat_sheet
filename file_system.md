- UUIDの確認
    - $ blkid
    - $ ls -l /dev/disk/by-uuid/

- UUIDの変更
    -  /dev/sdb1のUUIDを新たに生成したUUIDに変更する
    `$ tune2fs -U 'uuidgen' /dev/sdb1`

- カーネルがサポートしているファイルシステムの確認
    - `$ cat /proc/filesystems`

- 現在マウントされているファイルシステムの確認
    - `$ cat /etc/mtab`
    - `$ cat /proc/mounts`
    - `$ mount` (マウントされているデバイスの一覧)

- マウント/アンマウント
    - `$ mount /dev/sdb1 /data`  
    /dataをマウントポイントに/dev/sdb1をマウント
    - `$ mount -o remount /data`  
    /dataを再マウント

- スワップ
    - /dev/sda3にスワップ領域を作成    
    `$ mkswap -c /dev/sda3`  
    -c, --check: 作成前に不良ブロックのチェックをする
    - 500MBのファイルにスワップ領域を作成
        1. `$ dd if=/dev/zero of=/tmp/swapfile bs=1M count=500`
        2. `$ mkswap /tmp/swapfile`
        3. `$ shmod 600 /tmp/swapfile`

    - 有効化/無効化
        - 有効化: `$ swapon`
        - 無効化: `$ swapoff`

    - アクティブなスワップ領域の確認
        - `$ swapon -s`