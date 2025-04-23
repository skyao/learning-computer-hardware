---
title: "PVE 8.1 驱动"
linkTitle: "PVE 8.1"
weight: 30
date: 2022-03-10
description: >
  在 PVE 8.1 上安装 Mellanox MCX4121A 网卡的驱动
---

## 安装驱动

pve8.1 是基于 debian12 的，因此驱动安装方式和 debian 12 非常类似。同样下载驱动，然后执行:

```bash
./mlnxofedinstall --without-fw-update --with-nvmf --with-nfsrdma --ovs-dpdk --distro debian12.1

......
Do you want to continue?[y/N]:y

Checking SW Requirements...
One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
pkg-config libnl-3-dev libgfortran5 flex m4 graphviz tcl ifupdown libltdl-dev uuid-runtime libnl-route-3-dev swig bison autoconf quilt gfortran lsb-base autotools-dev debhelper chrpath libipsec-mb1 automake tk
Failed command: apt-get install -y -o Dpkg::Options::='--force-confdef' -o Dpkg::Options::='--force-confold' pkg-config libnl-3-dev libgfortran5 flex m4 graphviz tcl ifupdown libltdl-dev uuid-runtime libnl-route-3-dev swig bison autoconf quilt gfortran lsb-base autotools-dev debhelper chrpath libipsec-mb1 automake tk
See /tmp/MLNX_OFED_LINUX.60098.logs/general.log# 
```

会遇到失败，按照提示打开 `/tmp/MLNX_OFED_LINUX.60098.logs/general.log` 

单独执行 apt install 命令看看：

```bash
apt-get install -y -o Dpkg::Options::='--force-confdef' -o Dpkg::Options::='--force-confold' pkg-config libnl-3-dev libgfortran5 flex m4 graphviz tcl ifupdown libltdl-dev uuid-runtime libnl-route-3-dev swig bison autoconf quilt gfortran lsb-base autotools-dev debhelper chrpath libipsec-mb1 automake tk

......
W: (pve-apt-hook) !! WARNING !!
W: (pve-apt-hook) You are attempting to remove the meta-package 'proxmox-ve'!
W: (pve-apt-hook) 
W: (pve-apt-hook) If you really want to permanently remove 'proxmox-ve' from your system, run the following command
W: (pve-apt-hook) 	touch '/please-remove-proxmox-ve'
W: (pve-apt-hook) run apt purge proxmox-ve to remove the meta-package
W: (pve-apt-hook) and repeat your apt invocation.
```

按照提示，执行命令手工删除 proxmox-ve:

```bash
$ touch '/please-remove-proxmox-ve'
$ apt purge proxmox-ve

Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  proxmox-ve*
0 upgraded, 0 newly installed, 1 to remove and 0 not upgraded.
After this operation, 27.6 kB disk space will be freed.
Do you want to continue? [Y/n] y
W: (pve-apt-hook) '/please-remove-proxmox-ve' exists, proceeding with removal of package 'proxmox-ve'
(Reading database ... 121504 files and directories currently installed.)
Removing proxmox-ve (8.1.0) ...
(Reading database ... 121498 files and directories currently installed.)
Purging configuration files for proxmox-ve (8.1.0) ...
```

然后再次执行前面的 apt install 命令，就可以正常安装了。

再次执行 ./mlnxofedinstall 命令，提示需要加 `--force`，最后执行的命令是：

```bash
./mlnxofedinstall --without-fw-update --with-nvmf --with-nfsrdma --ovs-dpdk --distro debian12.1 --force
```

成功安装好驱动。

不幸的是看到了这个一个提示，NFSoRDMA 不支持 6.5.11-7-pve 内核。

```bash
WARNING: NFSoRDMA is not supported over kernel 6.5.11-7-pve, will continue installation without it.
```

## 故障

但是，重启之后发现驱动不可用：

```bash
➜  ~ lsmod | grep mlx
mlxdevm               184320  0
mlxfw                  36864  0
mlx_compat             20480  6 ib_ipoib,mlxdevm,ib_umad,ib_core,ib_uverbs,ib_cm
➜  ~ /etc/init.d/openibd

Usage: openibd {start|force-start|stop|force-stop|restart|force-restart|status}

➜  ~ /etc/init.d/openibd restart
Unloading HCA driver:                                      [  OK  ]
Failed loading kernel module mlx5_ib:                      [FAILED]
Loading Mellanox MLX5_IB HCA driver:                       [FAILED]
Failed loading kernel module mlx5_core:                    [FAILED]
Loading Mellanox MLX5 HCA driver:                          [FAILED]
Loading HCA driver and Access Layer:                       [FAILED]

Please run /usr/sbin/sysinfo-snapshot.py to collect the debug information
and open an issue in the http://support.mellanox.com/SupportWeb/service_center/SelfService
```

google了一下，发现这个讨论：

https://forum.proxmox.com/threads/upgrade-7-to-8-connect-4-dkms-module-installed.139297/

看样子似乎不需要安装额外的驱动，用内核自带的就好了。

