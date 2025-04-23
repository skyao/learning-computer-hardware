---
title: "ubuntu20.04上安装驱动"
linkTitle: "ubuntu20.04"
weight: 20
date: 2022-03-10
description: >
  在 ubuntu 20.04上安装hp544+网卡驱动
---

## 准备工作

检查当前默认驱动：

```bash
$ sudo su
$ modinfo mlx4_core | grep version
version:        4.0-0
srcversion:     CD88194143D98D15E719CD7
vermagic:       5.4.0-94-generic SMP mod_unload modversions

$ modinfo mlx4_core | grep ^version:|sed 's/version: * //g'
4.0-0
```

在操作前，网卡最好连接好网线，否则ifconfig会看不到网卡信息，或者需要加 -a 参数：

```bash
$ ifconfig -a

ens1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.0.20  netmask 255.255.255.0  broadcast 10.0.0.255
        inet6 fe80::4a0f:cfff:fef7:89c1  prefixlen 64  scopeid 0x20<link>
        ether 48:0f:cf:f7:89:c1  txqueuelen 1000  (Ethernet)
        RX packets 2196  bytes 166931 (166.9 KB)
        RX errors 0  dropped 1577  overruns 0  frame 0
        TX packets 418  bytes 48817 (48.8 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

$ ethtool -i ens1
driver: mlx4_en
version: 4.0-0
firmware-version: 2.42.5700
expansion-rom-version: 
bus-info: 0000:b3:00.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: yes
```

查看HP544+网卡的设备信息：

```bash
$ lspci -vvv
$ lspci | grep Mellanox
b3:00.0 Network controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]

$ lspci -vv -s b3:00.0 | grep "Part number" -A 3
			[PN] Part number: 764285-B21
			[EC] Engineering changes: A3
			[SN] Serial number: IL254902M0
			[V0] Vendor specific: Alom FDR x8 13W
```

### 更新驱动

从其他机器上复制文件:

```bash
scp ./MLNX_OFED_LINUX-4.9-6.0.6.0-ubuntu20.04-x86_64.tgz sky@192.168.0.10:/home/sky
```

解压缩：

```bash
tar -xvf MLNX_OFED_LINUX-4.9-6.0.6.0-ubuntu20.04-x86_64.tgz
cd MLNX_OFED_LINUX-4.9-6.0.6.0-ubuntu20.04-x86_64
```

执行安装命令：

```bash
$ sudo ./mlnxofedinstall --without-fw-update

Logs dir: /tmp/MLNX_OFED_LINUX.18504.logs
General log file: /tmp/MLNX_OFED_LINUX.18504.logs/general.log

Below is the list of MLNX_OFED_LINUX packages that you have chosen
(some may have been added by the installer due to package dependencies):

ofed-scripts
mlnx-ofed-kernel-utils
mlnx-ofed-kernel-dkms
rshim-dkms
iser-dkms
isert-dkms
srp-dkms
libibverbs1
ibverbs-utils
libibverbs-dev
libibverbs1-dbgmlnxofedinstall
libmlx4-1
libmlx4-dev
libmlx4-1-dbg
libmlx5-1
libmlx5-dev
libmlx5-1-dbg
libibumad
libibumad-static
libibumad-devel
ibacm
ibacm-dev
librdmacm1
librdmacm-utils
librdmacm-dev
mstflint
ibdump
libibmad
libibmad-static
libibmad-devel
libopensm
opensm
opensm-doc
libopensm-devel
infiniband-diags
infiniband-diags-compat
mft
kernel-mft-dkms
libibcm1
libibcm-dev
perftest
ibutils2
libibdm1
ibutils
ar-mgr
dump-pr
ibsim
ibsim-doc
ucx
sharp
hcoll
knem-dkms
knem
openmpi
mpitests
libdapl2
dapl2-utils
libdapl-dev
srptools
mlnx-ethtool
mlnx-iproute2

This program will install the MLNX_OFED_LINUX package on your machine.
Note that all other Mellanox, OEM, OFED, RDMA or Distribution IB packages will be removed.
Those packages are removed due to conflicts with MLNX_OFED_LINUX, do not reinstall them.

Do you want to continue?[y/N]:y

Checking SW Requirements...
One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
quilt automake dpatch libgfortran4 flex dkms gcc make autoconf chrpath tcl gfortran swig autotools-dev graphviz tk m4 libnl-route-3-200 libltdl-dev debhelper bison pkg-config
Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...

Installing rshim-dkms-1.18...
Installing iser-dkms-4.9...
Installing isert-dkms-4.9...
Installing srp-dkms-4.9...
Installing libibverbs1-41mlnx1...
Installing ibverbs-utils-41mlnx1...
Installing libibverbs-dev-41mlnx1...
Installing libibverbs1-dbg-41mlnx1...
Installing libmlx4-1-41mlnx1...
Installing libmlx4-dev-41mlnx1...
Installing libmlx4-1-dbg-41mlnx1...
Installing libmlx5-1-41mlnx1...
Installing libmlx5-dev-41mlnx1...
Installing libmlx5-1-dbg-41mlnx1...
Installing libibumad-43.1.1.MLNX20200211.078947f...
Installing libibumad-static-43.1.1.MLNX20200211.078947f...
Installing libibumad-devel-43.1.1.MLNX20200211.078947f...
Installing ibacm-41mlnx1...
Installing ibacm-dev-41mlnx1...
Installing librdmacm1-41mlnx1...
Installing librdmacm-utils-41mlnx1...
Installing librdmacm-dev-41mlnx1...
Installing mstflint-4.14.0...
Installing ibdump-6.0.0...
Installing libibmad-5.4.0.MLNX20190423.1d917ae...
Installing libibmad-static-5.4.0.MLNX20190423.1d917ae...
Installing libibmad-devel-5.4.0.MLNX20190423.1d917ae...
Installing libopensm-5.7.2.MLNX20201014.9378048...
Installing opensm-5.7.2.MLNX20201014.9378048...
Installing opensm-doc-5.7.2.MLNX20201014.9378048...
Installing libopensm-devel-5.7.2.MLNX20201014.9378048...
Installing infiniband-diags-5.6.0.MLNX20200211.354e4b7...
Installing infiniband-diags-compat-5.6.0.MLNX20200211.354e4b7...
Installing mft-4.15.1...
Installing kernel-mft-dkms-4.15.1...
Installing libibcm1-41mlnx1...
Installing libibcm-dev-41mlnx1...
Installing perftest-4.5.0.mlnxlibs...
Installing ibutils2-2.1.1...
Installing libibdm1-1.5.7.1...
Installing ibutils-1.5.7.1...
Installing ar-mgr-1.0...
Installing dump-pr-1.0...
Installing ibsim-0.10...
Installing ibsim-doc-0.10...
Installing ucx-1.8.0...
Installing sharp-2.1.2.MLNX20200428.ddda184...
Installing hcoll-4.4.2968...
Installing knem-dkms-1.1.4.90mlnx1...
Installing knem-1.1.4.90mlnx1...
Installing openmpi-4.0.3rc4...
Installing mpitests-3.2.20...
Installing libdapl2-2.1.10.1.mlnx...
Installing dapl2-utils-2.1.10.1.mlnx...
Installing libdapl-dev-2.1.10.1.mlnx...
Installing srptools-41mlnx1...
Installing mlnx-ethtool-5.4...
Installing mlnx-iproute2-5.4.0...
Selecting previously unselected package mlnx-fw-updater.
(Reading database ... 87526 files and directories currently installed.)
Preparing to unpack .../mlnx-fw-updater_4.9-4.1.7.0_amd64.deb ...
Unpacking mlnx-fw-updater (4.9-4.1.7.0) ...
Setting up mlnx-fw-updater (4.9-4.1.7.0) ...

Added 'RUN_FW_UPDATER_ONBOOT=no to /etc/infiniband/openib.conf

Installation passed successfully
To load the new driver, run:
/etc/init.d/openibd restart
```

重启设备驱动：


```bash
/etc/init.d/openibd restart

Unloading HCA driver:                                      [  OK  ]
Loading HCA driver and Access Layer:                       [  OK  ]
```

重启机器，然后检验：

```bash
$ modinfo mlx4_core | grep version

version:        4.9-6.0.6
srcversion:     B7B1BFEEF8DC7BE5A999C14
vermagic:       5.4.0-94-generic SMP mod_unload modversions 
```

可以看到现在的驱动版本已经从默认安装的 4.0-0 变成了 4.9-6.0.6 。

### 5.15内核

尝试在 ubuntu 20.04 上更新内核到 5.15.0-58， 然后在安装 MLNX_OFED 驱动时报错：

```bash
Copying build sources from '/var/lib/dkms/mlnx-ofed-kernel/4.9/build/../build' to '/usr/src/ofa_kernel/5.15.0-58-generic' ...
/bin/cp: cannot stat 'Module*.symvers': No such file or directory
```

目前 MLNX_OFED 不支持高版本的内核，测试验证过的只有 5.4 内核（也就是 ubuntu 20.04 默认的内核）。如果更新内核，就只能使用默认的 4.0.0 版本的驱动了，无法更新 4.9 版本。貌似 nvidia 也不准备继续支持。

尝试了一下在 5.4 内核上，更新 MLNX_OFED_LINUX-4.9-6.0.6.0 非常顺利。

参考：

- https://forums.developer.nvidia.com/t/failed-to-install-mlnx-ofed-kernel-dkms-deb-with-version-4-9-4-1-7-0/205889/2 
- https://docs.nvidia.com/networking/display/MLNXOFEDv494170/General+Support+in+MLNX_OFED#GeneralSupportinMLNX_OFED-MLNX_OFEDSupportedOperatingSystems
- https://bugs.launchpad.net/ubuntu/+source/linux/+bug/1926938

## 总结

hp544+ 这种 cx3 网卡，比较尴尬，驱动更新基本要停了，对新内核的支持也处于放弃状态。目前只能选择：

- 用 ubuntu 20.04 + 5.4 内核，驱动升级到 MLNX_OFED_LINUX-4.9-6.0.6.0 
- 用 ubuntu 20.04 + 5.15 内核，驱动继续使用默认自带的 4.0.0 版本。

考虑到目前对最新版本内核没有特别的需求，我选择第一个方案，暂时维持系统在最稳定的状态：ubuntu 20.04 + 5.4 内核 + MLNX_OFED_LINUX-4.9-6.0.6.0 ，都是有官方支持做保障的。

## 参考资料

- [家用万兆网络指南 1 - 不如先来个最简单的100G网络](https://zhuanlan.zhihu.com/p/74082377) 
- [Mellanox网卡驱动安装指南 Mellanox OFED](https://blog.51cto.com/zhangyy/4632975)
