- UUIDの確認
    - `$ blkid`
    - `$ ls -l /dev/disk/by-uuid/`
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
- ファイルシステムの管理
    - ext2/ext3/ext4
        - パラメータ設定: `$ tune2fs`
            - -c 回数: ファイルシステムチェックが行われるまでの最大マウント回数
            - -C 回数: 現在のマウント回数
            - -i 間隔: ファイルシステムチェックの間隔(60->60日, 2m->2か月, 8w->8週間)
            - -j: ext2からext3へ変換する
            - -m 領域(%): rootユーザ用予約領域のサイズを変更
            - -l: スーパーブロックの内容を表示
            - -L ラベル: ボリュームラベルを設定する
            - -U UUID: 指定したUUIDに変更
    - XFS
        - パラメータ設定: `$ xfs_admin`
            - -u: UUIDを表示
            - -l: ラベルを表示
            - -L ラベル: ラベルを設定
            - -U UUID: UUIDを設定
        - 情報表示: `$ xfs_info`
        - バックアップ: `$ xfsdump`
        - リストア: `$ xfsrestore`
    - Btrfs
        - `$ btrfs コマンド サブコマンド`
            - `filesystem show`: 情報表示
            - `filesystem df`: 使用状況表示
            - `filesystem label`: ラベル設定
            - `filesystem resize`: サイズ変更
            - `subvolume create`: サブボリュームを作成
            - `subvolume delete`: サブボリュームの削除
            - `subvolume list`: サブボリュームの一覧表示
            - `subvolume snapshot`: サブボリュームのスナップショットを作成
            - `subvolume show`: サブボリュームの情報表示
            - サブボリュームの作成とマウント
                - 作成: `$ btrfs subvolume create /mnt/sub1`
                - IDの確認: `$ btrfs subvolume list /mnt`
                - マウント: `$ mount -t btrfs -o subvolid=確認したID /dev/sdb1 /data`
        - Btrfsへの変換: `$ btrfs-convert`
- ISOファイル
    - CD-ROMやDVDで使用するファイルシステムの規格に基づきディレクト階層を1つのファイルにまとめたもの
    - CD-ROMやDVDのISO9660イメージファイルを作成: `$ mkisofs [オプション] ディレクトリ`

    | オプション | 説明 |
    | ---- | ---- |
    | -J | MicrosoftWindowsからも読めるようにISO9660にJoliet拡張を追加 |
    | -R | UNIX(POSIX)ファイルシステムに対応したRockRidge拡張を追加 |
    | -o | 出力先ファイルを指定 |
    | -T | TRANS.TBLファイルを各ディレクトリに作成 |

    ＊ TRANS.TBL: RockRidge拡張やJoliet拡張に対応していないシステムのための短縮形ファイル名と元の長いファイル名の変換テーブル

- SMART(Self-Monitoring Analysis and Reporting Technology System)
    - smartmontools(代表的なLinuxのSMART対応ソフトウェア)
        - `$ smartctl オプション`
            - -a, --all: SMART情報を表示
            - -c, --capabilities: テストの対応情報を表示
            - -i, --info: 各種情報を表示
            - -t , --test: 自己診断を実施
            - -l, --long: 最近のテスト結果を一覧表示
            - -H, --health: 状態を表示
            - --scan: 認識されているデバイスを表示
        - SMART対応デバイスのSMARTを有効化する: `$ smartctl --smart=on デバイスのパス`
        - 自己診断
            - `$ smartctl -t short`: 簡易的なチェック
            - `$ smartctl -t long`: 完全なチェック
            - `$ smartctl -t conveyance`: デバイスの輸送中に破損しなかったかをチェック
- LVM
    - 物理ボリューム
        - 作成: `$ pvcreate`
        - 情報表示: `$ pvdisplay`
        - 内容の移動: `$ pvmove 移動元 移動先`
        - 削除: `$ pvremove`
        - 状態表示: `$ pvscan`
        - 情報出力: `$ pvs`
    - ボリュームグループ
        - 作成: `$ vgcreate`
        - 属性変更: `$ vgchange`
        - 情報表示: `$ vgdisplay`
        - 拡張(PV追加): `$ vgextend`
        - 縮小(PV削除): `$ vgreduce`
        - 削除: `$ vgremove`
        - インポート: `$ vgimport`
        - エクスポート: `$ vgexport`
        - 統合: `$ vgmerge`
        - リネーム: `$ vgrename`
        - 状態表示: `$ vgscan`
        - 情報表示: `$ vgs`
    - 論理ボリューム
        - 作成: `$ lvcreate`
        - 情報表示: `$ lvdisplay`
        - 属性変更: `$ lvchange`
        - 拡張: `$ lvextend`
        - 縮小: `$ lvreduce`
        - 削除: `$ lvremove`
        - リネーム: `$ lvrename`
        - 状態表示: `$ lvscan`
        - 情報表示: `$ lvs`