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
        - `$ cat /proc/swaps`

- ブロックデバイスの一覧表示
    - `$ lsblk`

- ファイルシステムの作成
    - ext2ファイルシステム
        - `$ mkfs.ext2`
        - `$ mke2fs`
    - ext3ファイルシステム
        - `$ mkfs.ext3`
        - `$ mke2fs -j`
    - ブロックサイズ4096バイト, 不良ブロックチェックを行い, ext3ファイルシステムを作成  
        - `$ mke2fs -b 4096 -c -j /dev/sdb2`
    - ext4ファイルシステム
        - `$ mkfs.ext4`
        - `$ mke2fs -t ext4`
    - XFSファイルシステム
        - `$ mkfs.xfs`
    - Btrfsファイルシステム
        - `$ mkfs.btrfs`
        - `$ btrfs-convert` (ext2/ext3/ext4ファイルシステムをBtrfsに変換)
    - cramfsファイルシステム
        - `$ mkfs.cramfs`
    - FAT/VFATファイルシステム
        - `$ mkfs.vfat`
    - MINIX FSファイルシステム
        - `$ mkfs.minix`

- XFSファイルシステム
    - SGI社が開発したジャーナリングファイルシステム
    - 最大ボリュームサイズ: 8EB
    - 最大ファイルサイズ: 8EB
    - カーネルがXFS対応になっているかどうかの確認
        - `$ modprobe xfs`
        - `$ grep xfs /proc/filesystems`

- ファイルシステムのチェック
    - `$ fsck`  
    各ファイルシステムごとのチェックプログラムのフロントエンドとして機能
    -t でファイルシステムの種類を指定
    - `$ e2fsck`  
    ext2/ext3/ext4ファイルシステムを対象にしたチェックプログラム
    - `$ shutdown -F -r now`  
    マウント中のファイルシステムのチェックはshutdownコマンドに-Fオプションをつける
    - `$ xfs_repair`  
    XFSファイルシステムのチェック

