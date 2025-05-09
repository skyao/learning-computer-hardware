---
title: "debian 12 安装驱动"
linkTitle: "debian 12"
weight: 30
date: 2025-04-23
description: >
  在debian12上安装华为sp350网卡的驱动
---

> 2025-04-23 更新： debian12 已经升级到 12.9 版本，网卡驱动版本为最新的 24.07-0.6.1.0 版本。

## 准备工作

### 查看默认驱动

这是debian12自带的默认驱动情况：

```bash
$ lsmod | grep mlx

mlx5_ib               405504  0
ib_uverbs             172032  1 mlx5_ib
ib_core               438272  2 ib_uverbs,mlx5_ib
mlx5_core            1691648  1 mlx5_ib
mlxfw                  36864  1 mlx5_core
psample                20480  1 mlx5_core
pci_hyperv_intf        16384  1 mlx5_core
```

mlx5_core 的详细信息：

```bash
 $ modinfo mlx5_core               
filename:       /lib/modules/6.1.0-31-amd64/kernel/drivers/net/ethernet/mellanox/mlx5/core/mlx5_core.ko
license:        Dual BSD/GPL
description:    Mellanox 5th generation network adapters (ConnectX series) core driver
author:         Eli Cohen <eli@mellanox.com>
alias:          auxiliary:mlx5_core.eth
alias:          pci:v000015B3d0000A2DFsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2DCsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D6sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D3sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D2sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001025sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001023sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001021sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Fsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Esv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Dsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Csv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Bsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Asv*sd*bc*sc*i*
alias:          pci:v000015B3d00001019sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001018sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001017sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001016sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001015sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001014sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001013sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001012sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001011sv*sd*bc*sc*i*
alias:          auxiliary:mlx5_core.eth-rep
depends:        psample,pci-hyperv-intf,mlxfw
retpoline:      Y
intree:         Y
name:           mlx5_core
vermagic:       6.1.0-31-amd64 SMP preempt mod_unload modversions
sig_id:         PKCS#7
signer:         Debian Secure Boot CA
sig_key:        32:A0:28:7F:84:1A:03:6F:A3:93:C1:E0:65:C4:3A:E6:B2:42:26:43
sig_hashalgo:   sha256
signature:      0B:7F:71:B7:60:71:43:5A:78:9B:7A:A0:9B:80:CC:B1:22:DE:6E:01:
                97:CF:38:0D:14:6A:A7:5D:A8:E5:84:DE:89:6E:28:78:73:90:D1:CF:
                AD:87:4E:0D:92:A9:32:68:36:B8:1A:8A:AF:8E:38:73:85:B8:42:CC:
                77:63:4B:00:06:B9:07:33:5C:63:62:00:D8:4A:FD:64:71:DB:CC:CB:
                20:4B:47:12:F9:C5:38:B8:DA:02:72:8D:55:CE:BF:5D:0A:BE:73:22:
                B5:8B:C7:A6:71:49:43:23:EA:23:E2:7B:0D:F4:7D:22:FE:36:E8:00:
                64:F2:93:9D:45:54:49:09:58:0E:DE:54:A8:17:1D:66:2E:21:47:1D:
                C7:A8:2F:41:41:AC:80:0C:30:7B:21:CD:B5:05:93:69:50:1A:65:DB:
                03:D5:C8:06:5D:CE:5B:45:6C:F2:D3:6D:58:A8:56:C3:46:89:05:FA:
                5E:FD:04:EA:29:5F:1B:5A:E6:40:5D:7E:46:D5:61:30:AC:E8:83:A8:
                67:E6:05:26:91:7F:43:31:E3:70:A1:CF:E9:E5:65:5A:46:AE:86:F4:
                84:6F:A8:18:A7:C4:97:67:71:76:93:4C:7E:3F:68:65:3D:E9:A1:0C:
                FB:BB:94:58:52:AE:9F:F0:9D:74:FC:60:10:EF:4A:83
parm:           debug_mask:debug mask: 1 = dump cmd data, 2 = dump cmd exec time, 3 = both. Default=0 (uint)
parm:           prof_sel:profile selector. Valid range 0 - 2 (uint)
```

### 下载驱动

下载地址：

https://network.nvidia.com/products/infiniband-drivers/linux/mlnx_ofed/

选择对应的 debian 版本，最新的 24.10-2.1.8.0 版本已经提供对 debian 12.5 版本的支持了：

![mlnx_ofed_download](images/mlnx_ofed_download.png)

下载得到 **MLNX_OFED_LINUX-24.10-2.1.8.0-debian12.5-x86_64.tgz** 文件， scp 传到 debian 12 下。

### 关闭 secure boot

需要在物理机或者虚拟机的 bios 中关闭了 secure boot，会和最新 24.10-2.1.8.0 版本的 mlnx_ofed 驱动冲突。

pve虚拟机中如图：

"Device Manager" -> "Secure Boot Configuration" -> "Attempt Secure Boot" -> 取消勾选。

![disable-secure-boot](images/disable-secure-boot.jpg)

否则安装最新版本的驱动后会报错而导致网卡无法使用。

## 安装驱动

```bash
su root
tar xvf MLNX_OFED_LINUX-24.10-2.1.8.0-debian12.5-x86_64.tgz
cd MLNX_OFED_LINUX-24.10-2.1.8.0-debian12.5-x86_64
```

设置 PATH 否则默认 PATH 会找不到某些重要的命令而失败:

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

配置代理，加快下载速度:

```bash
export all_proxy=socks5://192.168.0.1:7891;export http_proxy=http://192.168.0.1:7890;export https_proxy=http://192.168.0.1:7890;export no_proxy=127.0.0.1,localhost,local,.local,.lan,192.168.0.0/16,10.0.0.0/16
```

开始安装:

```bash
./mlnxofedinstall --without-fw-update --with-nvmf --with-nfsrdma --ovs-dpdk --distro debian12.5
```

注意对于某些版本的驱动要加 `--distro debian12.5`, 否则可能会报错：

```bash
Error: The current MLNX_OFED_LINUX is intended for debian12.1
```

这是因为我安装debian12时版本已经是 12.9了，而最新的 24.10-2.1.8.0 驱动针对 debian 12.5 的打包：

```bash
./mlnxofedinstall --print-distro
debian12.9
```

`--with-nvmf --with-nfsrdma --ovs-dpdk` 这三个参数是可选的，我增加这三个参数主要是为了要学习测试这几个功能。

安装过程如下：

```bash
$./mlnxofedinstall --without-fw-update --with-nvmf --with-nfsrdma --ovs-dpdk

Logs dir: /tmp/MLNX_OFED_LINUX.3039.logs
General log file: /tmp/MLNX_OFED_LINUX.3039.logs/general.log

Below is the list of MLNX_OFED_LINUX packages that you have chosen
(some may have been added by the installer due to package dependencies):

ofed-scripts
mlnx-tools
mlnx-ofed-kernel-utils
mlnx-ofed-kernel-dkms
iser-dkms
isert-dkms
srp-dkms
mlnx-nvme-dkms
rdma-core
libibverbs1
ibverbs-utils
ibverbs-providers
libibverbs-dev
libibverbs1-dbg
libibumad3
libibumad-dev
ibacm
librdmacm1
rdmacm-utils
librdmacm-dev
ibdump
libibmad5
libibmad-dev
libopensm
opensm
opensm-doc
libopensm-devel
libibnetdisc5
infiniband-diags
mft
kernel-mft-dkms
perftest
ibutils2
ibsim
ibsim-doc
ucx
sharp
hcoll
knem-dkms
knem
openmpi
mpitests
libxpmem0
libxpmem-dev
srptools
mlnx-ethtool
mlnx-iproute2
rshim
ibarr
libopenvswitch
openvswitch-common
openvswitch-switch

This program will install the MLNX_OFED_LINUX package on your machine.
Note that all other Mellanox, OEM, OFED, RDMA or Distribution IB packages will be removed.
Those packages are removed due to conflicts with MLNX_OFED_LINUX, do not reinstall them.

Do you want to continue?[y/N]:y

Checking SW Requirements...
Removing old packages...
Installing new packages
Installing ofed-scripts-24.10.OFED.24.10.2.1.8...
Installing mlnx-tools-24.10...
Installing mlnx-ofed-kernel-utils-24.10.OFED.24.10.2.1.8.1...
Installing mlnx-ofed-kernel-dkms-24.10.OFED.24.10.2.1.8.1...
Installing iser-dkms-24.10.OFED.24.10.2.1.8.1...
Installing isert-dkms-24.10.OFED.24.10.2.1.8.1...
Installing srp-dkms-24.10.OFED.24.10.2.1.8.1...
Installing mlnx-nvme-dkms-24.10.OFED.24.10.2.1.8.1...
Installing rdma-core-2410mlnx54...
Installing libibverbs1-2410mlnx54...
Installing ibverbs-utils-2410mlnx54...
Installing ibverbs-providers-2410mlnx54...
Installing libibverbs-dev-2410mlnx54...
Installing libibverbs1-dbg-2410mlnx54...
Installing libibumad3-2410mlnx54...
Installing libibumad-dev-2410mlnx54...
Installing ibacm-2410mlnx54...
Installing librdmacm1-2410mlnx54...
Installing rdmacm-utils-2410mlnx54...
Installing librdmacm-dev-2410mlnx54...
Installing ibdump-6.0.0...
Installing libibmad5-2410mlnx54...
Installing libibmad-dev-2410mlnx54...
Installing libopensm-5.21.0.MLNX20241126.d9aa3dff...
Installing opensm-5.21.0.MLNX20241126.d9aa3dff...
Installing opensm-doc-5.21.0.MLNX20241126.d9aa3dff...
Installing libopensm-devel-5.21.0.MLNX20241126.d9aa3dff...
Installing libibnetdisc5-2410mlnx54...
Installing infiniband-diags-2410mlnx54...
Installing mft-4.30.1...
Installing kernel-mft-dkms-4.30.1.113...
Installing perftest-24.10.0...
Installing ibutils2-2.1.1...
Installing ibsim-0.12...
Installing ibsim-doc-0.12...
Installing ucx-1.18.0...
Installing sharp-3.9.0.MLNX20241029.7a20b607...
Installing hcoll-4.8.3230...
Installing knem-dkms-1.1.4.90mlnx3...
Installing knem-1.1.4.90mlnx3...
Installing openmpi-4.1.7rc1...
Installing mpitests-3.2.24...
Installing libxpmem0-2.7...
Installing libxpmem-dev-2.7...
Installing srptools-2410mlnx54...
Installing mlnx-ethtool-6.9...
Installing mlnx-iproute2-6.10.0...
Installing rshim-2.1.10...
Installing ibarr-0.1.3...
Installing libopenvswitch-2.17.8...
Installing openvswitch-common-2.17.8...
Installing openvswitch-switch-2.17.8...
Selecting previously unselected package mlnx-fw-updater.
(Reading database ... 74800 files and directories currently installed.)
Preparing to unpack .../mlnx-fw-updater_24.10-2.1.8.0_amd64.deb ...
Unpacking mlnx-fw-updater (24.10-2.1.8.0) ...
Setting up mlnx-fw-updater (24.10-2.1.8.0) ...

Added 'RUN_FW_UPDATER_ONBOOT=no to /etc/infiniband/openib.conf

Skipping FW update.
Device (01:00.0):
        01:00.0 Ethernet controller: Mellanox Technologies MT28800 Family [ConnectX-5 Ex]
        Link Width: x16
        PCI Link Speed: 16GT/s

Device (01:00.1):
        01:00.1 Ethernet controller: Mellanox Technologies MT28800 Family [ConnectX-5 Ex]
        Link Width: x16
        PCI Link Speed: 16GT/s

Installation passed successfully
To load the new driver, run:
/etc/init.d/openibd restart
Note: In order to load the new nvme-rdma and nvmet-rdma modules, the nvme module must be reloaded.
```

重启之后, 24.01-0.3.3.1 和之前的版本就可以正常工作了。

### 报错：pci_hp_register failed

但最新的 24.07-0.6.1.0 版本会报错， `ip addr` 会发现 cx5 网卡不见了。

`dmesg` 查看，会发现有这样的错误提示：

```bash
pci_hp_register failed with error -16
```

如果升级 linix 内核，则会在升级时提示 "Your system has UEFI Secure Boot enabled"：

![](images/secure-boot-enabled-warnning.jpg)

我就是根据这个线索，去虚拟机的 bios 中关闭了 secure boot：

![](images/disable-secure-boot.jpg)

重启就正常了。

## 安装后处理

### 查看安装后的驱动信息

```bash
$ lsmod | grep mlx

mlx5_ib               495616  0
ib_uverbs             188416  1 mlx5_ib
ib_core               462848  2 ib_uverbs,mlx5_ib
mlx5_core            2441216  1 mlx5_ib
mlxfw                  36864  1 mlx5_core
psample                20480  1 mlx5_core
mlxdevm               188416  1 mlx5_core
mlx_compat             20480  6 mlxdevm,mlxfw,ib_core,ib_uverbs,mlx5_ib,mlx5_core
tls                   135168  1 mlx5_core
pci_hyperv_intf        16384  1 mlx5_core
```

mlx5_core 的详细信息：

```bash
$ modinfo mlx5_core                
filename:       /lib/modules/6.1.0-31-amd64/updates/dkms/mlx5_core.ko
alias:          auxiliary:mlx5_core.eth-rep
alias:          auxiliary:mlx5_core.eth
basedon:        Korg 6.8-rc4
version:        24.10-2.1.8
license:        Dual BSD/GPL
description:    Mellanox 5th generation network adapters (ConnectX series) core driver
author:         Eli Cohen <eli@mellanox.com>
srcversion:     78352976D87E8F24553D352
alias:          pci:v000015B3d0000A2DFsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2DCsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D6sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D3sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000A2D2sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001025sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001023sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001021sv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Fsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Esv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Dsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Csv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Bsv*sd*bc*sc*i*
alias:          pci:v000015B3d0000101Asv*sd*bc*sc*i*
alias:          pci:v000015B3d00001019sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001018sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001017sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001016sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001015sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001014sv*sd*bc*sc*i*
alias:          pci:v000015B3d00001013sv*sd*bc*sc*i*
alias:          auxiliary:mlx5_core.sf
depends:        mlxdevm,mlx_compat,tls,pci-hyperv-intf,psample,mlxfw
retpoline:      Y
name:           mlx5_core
vermagic:       6.1.0-31-amd64 SMP preempt mod_unload modversions
sig_id:         PKCS#7
signer:         DKMS module signing key
sig_key:        71:58:C0:4E:AD:2B:3B:86:A0:6C:5E:B4:1B:14:52:CB:B6:CA:D0:91
sig_hashalgo:   sha256
signature:      65:99:87:08:64:8C:B3:04:74:79:60:3E:83:4A:F8:48:96:C7:D4:3D:
                70:D6:32:B8:B3:6D:EE:CA:41:4D:CA:A4:8B:8F:60:79:17:81:21:40:
                41:87:E6:B9:25:C4:C8:1E:25:60:15:87:D3:F6:EA:D9:E6:CB:57:7B:
                DC:59:D1:C6:4E:6C:D3:80:BE:8E:D6:C0:1F:E3:1F:0F:07:8A:9B:E4:
                06:E7:7D:2C:27:C1:B6:47:01:5D:11:CC:DF:9A:14:63:0F:3F:84:6A:
                9F:22:AE:70:68:E8:7F:5F:AB:4D:DB:9B:C4:A1:E2:E6:9D:AD:E0:6C:
                BD:2D:50:4F:31:CA:29:C6:86:D0:D2:5E:B2:3F:44:7C:4E:23:A0:CD:
                E2:3C:2E:2A:DC:8E:7E:E1:B0:E8:7C:86:6D:78:29:E1:56:3A:91:BD:
                41:C4:40:09:0D:A1:75:70:0A:AC:44:AE:91:CB:26:64:1C:3C:F2:B5:
                E3:F6:0B:01:25:D9:D2:4E:5A:7D:EA:C4:86:4A:2F:93:0B:E5:AF:25:
                ED:D5:A3:50:99:94:A9:3B:8F:AD:B9:55:2D:A5:C3:E3:A5:A7:3B:F0:
                D0:A1:19:C9:8E:85:02:0E:A9:6A:54:94:41:C0:AE:E1:78:EB:D9:CB:
                A8:9E:9E:41:24:AF:D5:26:FF:DB:E5:08:0D:16:6B:0B
parm:           num_of_groups:Eswitch offloads number of big groups in FDB table. Valid range 1 - 1024. Default 15 (uint)
parm:           debug_mask:debug mask: 1 = dump cmd data, 2 = dump cmd exec time, 3 = both. Default=0 (uint)
parm:           prof_sel:profile selector. Valid range 0 - 4 (uint)
```

更新之后 mlx5_core 的版本从默认升级到 24.10-2.1.8 ：

```bash
$ modinfo mlx5_core |  grep version

version:        24.10-2.1.8
srcversion:     78352976D87E8F24553D352
vermagic:       6.1.0-31-amd64 SMP preempt mod_unload modversions
```

