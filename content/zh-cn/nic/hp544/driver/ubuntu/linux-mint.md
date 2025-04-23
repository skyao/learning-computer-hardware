---
title: "linux-mint上安装驱动"
linkTitle: "linux-mint"
weight: 30
date: 2022-03-10
description: >
  在 linux-mint 上安装hp544+网卡驱动
---



在基于 ubuntu 20.04 内核的 linux mint 20.02 版本上更新驱动。但奈何没能搞定，各种错误，最后放弃，只能继续使用自带的驱动。

> 以下记录仅作为归档。

-----

直接执行命令会报错，因为默认有linux发行版本的检查：

```bash
./mlnxofedinstall
Current operation system is not supported (linuxmint20.2)!
```

解决的方式之一是通过命令行参数 `--distro` 传递发行版本信息进去：

```bash
./mlnxofedinstall --distro ubuntu20.04

Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...
Failed to install mlnx-ofed-kernel-dkms DEB
Collecting debug info...
See /tmp/MLNX_OFED_LINUX.11004.logs/mlnx-ofed-kernel-dkms.debinstall.log
```

遇到报错，`/tmp/MLNX_OFED_LINUX.11004.logs/mlnx-ofed-kernel-dkms.debinstall.log` 中的信息为:

```bash
/usr/bin/dpkg -i --force-confnew --force-confmiss /home/sky/hp544/MLNX_OFED_LINUX-4.9-4.1.7.0-ubuntu20.04-x86_64/DEBS/MLNX_LIBS/mlnx-ofed-kernel-dkms_4.9-OFED.4.9.4.1.7.1_all.deb
Selecting previously unselected package mlnx-ofed-kernel-dkms.
(Reading database ... 322968 files and directories currently installed.)
Preparing to unpack .../mlnx-ofed-kernel-dkms_4.9-OFED.4.9.4.1.7.1_all.deb ...
Unpacking mlnx-ofed-kernel-dkms (4.9-OFED.4.9.4.1.7.1) ...
Setting up mlnx-ofed-kernel-dkms (4.9-OFED.4.9.4.1.7.1) ...
Loading new mlnx-ofed-kernel-4.9 DKMS files...
First Installation: checking all kernels...
Building only for 5.4.0-92-generic
Building for architecture x86_64
Building initial module for 5.4.0-92-generic
Error! Bad return status for module build on kernel: 5.4.0-92-generic (x86_64)
Consult /var/lib/dkms/mlnx-ofed-kernel/4.9/build/make.log for more information.
dpkg: error processing package mlnx-ofed-kernel-dkms (--install):
 installed mlnx-ofed-kernel-dkms package post-installation script subprocess returned error exit status 10
Errors were encountered while processing:
 mlnx-ofed-kernel-dkms
```

`/var/lib/dkms/mlnx-ofed-kernel/4.9/build/make.log` 文件中的信息：

```bash
Copying build sources from '/var/lib/dkms/mlnx-ofed-kernel/4.9/build/../build' to '/usr/src/ofa_kernel/5.4.0-92-generic' ...
/bin/cp: cannot stat 'Module*.symvers': No such file or directory
```

检查这台机器上的相关信息：

```bash
$ uname --all
Linux skyserver3 5.4.0-94-generic #106-Ubuntu SMP Thu Jan 6 23:58:14 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
$ ls /usr/src/
linux-headers-5.4.0-94  linux-headers-5.4.0-94-generic  mlnx-ofed-kernel-4.9  ofa_kernel  ofa_kernel-4.9
$ ls /lib/modules/
5.4.0-94-generic
$ locate Module.symvers
# 注意这里没有信息
```

对照成功安装驱动的ubuntu server 20.04 机器上的信息：

```bash
$ uname --all
Linux skywork2 5.4.0-94-generic #106-Ubuntu SMP Thu Jan 6 23:58:14 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
$ ls /usr/src/
iser-4.9                linux-headers-5.4.0-94          ofa_kernel-4.9
isert-4.9               linux-headers-5.4.0-94-generic  rshim-1.18
kernel-mft-dkms-4.15.1  mlnx-ofed-kernel-4.9            srp-4.9
knem-1.1.4.90mlnx1      ofa_kernel
$ ls /lib/modules/
5.4.0-94-generic
$ locate Module.symvers
/usr/src/linux-headers-5.4.0-94-generic/Module.symvers
/usr/src/ofa_kernel/5.4.0-94-generic/Module.symvers
/usr/src/ofa_kernel/5.4.0-94-generic/compat/build/Module.symvers
```

执行 `locate Module.symvers` 时发现没有列出信息：

```bash
$ locate Module.symvers
```



```bash
# 事实上python3已经安装好了
sudo apt-get install python3
# 但是python命令不存在，需要通过python-is-python3来把python命令按照python3来执行
sudo apt-get install python-is-python3
# 安装过程中需要用到 distutils
sudo apt-get install python3-distutils

$ 开始安装
$./mlnxofedinstall  --distro ubuntu20.04 --without-fw-update

Logs dir: /tmp/MLNX_OFED_LINUX.1976.logs
General log file: /tmp/MLNX_OFED_LINUX.1976.logs/general.log

Below is the list of MLNX_OFED_LINUX packages that you have chosen
(some may have been added by the installer due to package dependencies):

ofed-scripts
mlnx-ofed-kernel-utils
mlnx-ofed-kernel-dkms
......
This program will install the MLNX_OFED_LINUX package on your machine.
Note that all other Mellanox, OEM, OFED, RDMA or Distribution IB packages will be removed.
Those packages are removed due to conflicts with MLNX_OFED_LINUX, do not reinstall them.

Do you want to continue?[y/N]:y

Checking SW Requirements...


One or more required packages for installing MLNX_OFED_LINUX are missing.
Attempting to install the following missing packages:
gfortran graphviz tcl swig chrpath dpatch debhelper libltdl-dev libgfortran4 tk automake quilt autotools-dev autoconf


Removing old packages...
Installing new packages
Installing ofed-scripts-4.9...
Installing mlnx-ofed-kernel-utils-4.9...
Installing mlnx-ofed-kernel-dkms-4.9...


Error: mlnx-ofed-kernel-dkms installation failed!
Collecting debug info...

See:
	/tmp/MLNX_OFED_LINUX.1976.logs/mlnx-ofed-kernel-dkms.debinstall.log
Removing newly installed packages...
```

但奇怪的是 `/tmp/MLNX_OFED_LINUX.1976.logs/mlnx-ofed-kernel-dkms.debinstall.log` 日志文件中并没有报错，反而是显示 `DKMS: install completed.` 

```py
./mlnxofedinstall  --distro ubuntu20.04 
./mlnxofedinstall  --distro ubuntu20.04 --without-fw-update
./mlnxofedinstall --add-kernel-support --distro ubuntu20.04 --skip-repo
```

参考 https://docs.nvidia.com/networking/display/MLNXOFEDv494080/Installing+Mellanox+OFED

```bash
./mlnxofedinstall --without-dkms --add-kernel-support --kernel 3.13.0-85-generic --without-fw-update --force
```

失败但是日志没有错误信息，实在无力再拍错了。

退回去用自带 4.0.0 版本驱动，好处就是可以使用比较新的 5.15 内核。



