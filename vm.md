- KVM(Kernel-based Virtual Machine)
    - 仮想マシンを実行するための基盤OSとしてLinuxを使用するためのソフトウェア
    - CPUが仮想化支援機構に対応している必要がある(Intel VT-x, AMD AMD-V)
        - `$ lscpu | grep Virtualization`  
            -> `Virtualization:     VT-x`  
            -> `Virtualization:     AMD-V`
        - `$ grep -E 'vmx|svm' /proc/cpuinfo`
- 仮想マシンの作成と管理
    - KVM環境構築
        - 必要なパッケージのインストール(CentOS 7上)  
        `$ yum install libvirt bridge-utils qemu-kvm virt-manager virt-install`
        - libvirtdサービスの起動と有効化(KVMの仮想化をサポートするサービス)  
        `$ systemctl start libvirtd`  
        `$ systemctl enable libvirtd`
        - NetworkManagerを用いてブリッジネットワークを構築  
        (仮想マシンのネットワークインターフェースをホストOSのネットワークインターフェースと同じネットワーク上に配置する仕組み)  
            1. `$ nmcli con add type bridge con-name br0 ifname br0`
            2. `$ nmcli con mod br0 bridge.stp no`
            3. `$ nmcli con mod br0 ipv4.method manual ipv4.addresses "192.168.1.80/24" ipv4.gateway "192.168.1.1" ipv4.dns "192.168.1.1"`
    - 仮想マシンイメージの作成
        - インストール可能なOSの確認: `$ osinfo-query os | grep -i centos`
        - virt-installを用いたOSのインストール  
        ```
            $ virt-install \
            > --name vm_centos7 \                                   <- 仮想マシンの名前
            > --os-type linux \                                     <- ゲストOSのタイプ
            > --os-variant centos7.0 \                              <- ゲストOSの種類
            > --arch x86_64 \                                       <- アーキテクチャ
            > --vcpus 2 \                                           <- 割り当てる仮想CPU数
            > --memory 1024 \                                       <- 割り当てるメモリ(MB)
            > --network bridge=br0 \                                <- ブリッジネットワークに接続
            > --disk /var/lib/libvirt/images/centos7.img,size=20 \  <- ストレージのパスとサイズ(GB)
            > --location /data/CentOS7.iso \                        <- インストールメディアのパス
            > --nographics \                                        <- GUIを使わない
            > --extra-args="console=tty0 console=ttyS0,115200n8"    <- コンソール用パラメータ
        ```
        - GUI環境で仮想マシンを作成しOSをインストールする場合は `virt-manager` を用いる
    - 仮想マシンの操作
        - 起動: `$ virsh start 仮想マシン名`
        - 実行中の仮想マシン一覧: `$ virsh list`
        - 実行していないものも含めた仮想マシン一覧: `$ virsh list --all`
        - 仮想CPU情報の表示: `$ virsh vcpuinfo 仮想マシン名`
        - ゲストOSのコンソールに接続: `$ virsh console 仮想マシン名`
        - 停止: `$ virsh shutdown 仮想マシン名`
        - 編集: `$ virsh edit 仮想マシン名`