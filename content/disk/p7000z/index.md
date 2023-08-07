---
title: "爱国者p7000z 4t硬盘"
linkTitle: "爱国者p7000z"
weight: 1
date: 2021-03-10
description: 爱国者p7000z 4t pcie4.0 SSD硬盘在pve下的使用
---

## 问题描述

618 大促时低价从京东购买的爱国者 p7000z 4t pcie4.0 SSD 硬盘，发现在 pve 8.0 下无法使用，安装时报错：

```bash
kernel: nvme nvme0: Device not ready; aborting initialisation, CSTS=0x0
```

问题描述参考这里：

https://www.chiphell.com/forum.php?mod=redirect&goto=findpost&ptid=2524660&pid=52962287

我遇到的问题和他一样，报错相同，而且我只有一块硬盘也是如此报错。

## 解决方案

参考chh的帖子：

https://www.chiphell.com/thread-2524660-1-1.html

下载补丁，上传到 pve 所在机器，然后安装：

```bash
dpkg -i *.deb
```

安装过程如下：

```bash

dpkg: warning: downgrading linux-tools-6.2 from 6.2.16-6 to 6.2.16-4
(Reading database ... 153375 files and directories currently installed.)
Preparing to unpack linux-tools-6.2_6.2.16-4_amd64.deb ...
Unpacking linux-tools-6.2 (6.2.16-4) over (6.2.16-6) ...
dpkg: warning: downgrading linux-tools-6.2-dbgsym from 6.2.16-6 to 6.2.16-4
Preparing to unpack linux-tools-6.2-dbgsym_6.2.16-4_amd64.deb ...
Unpacking linux-tools-6.2-dbgsym (6.2.16-4) over (6.2.16-6) ...
dpkg: warning: downgrading pve-headers-6.2.16-4-pve from 6.2.16-5 to 6.2.16-4
Preparing to unpack pve-headers-6.2.16-4-pve_6.2.16-4_amd64.deb ...
Unpacking pve-headers-6.2.16-4-pve (6.2.16-4) over (6.2.16-5) ...
dpkg: warning: downgrading pve-kernel-6.2.16-4-pve from 6.2.16-5 to 6.2.16-4
Preparing to unpack pve-kernel-6.2.16-4-pve_6.2.16-4_amd64.deb ...
Unpacking pve-kernel-6.2.16-4-pve (6.2.16-4) over (6.2.16-5) ...
Preparing to unpack pve-kernel-libc-dev_6.2.16-4_amd64.deb ...
Unpacking pve-kernel-libc-dev (6.2.16-4) over (6.2.16-4) ...
Setting up linux-tools-6.2 (6.2.16-4) ...
Setting up linux-tools-6.2-dbgsym (6.2.16-4) ...
Setting up pve-headers-6.2.16-4-pve (6.2.16-4) ...
Setting up pve-kernel-6.2.16-4-pve (6.2.16-4) ...
Examining /etc/kernel/postinst.d.
run-parts: executing /etc/kernel/postinst.d/dkms 6.2.16-4-pve /boot/vmlinuz-6.2.16-4-pve
dkms: running auto installation service for kernel 6.2.16-4-pve.
Deprecated feature: REMAKE_INITRD (/var/lib/dkms/r8125/9.011.01/source/dkms.conf)
Deprecated feature: REMAKE_INITRD (/var/lib/dkms/r8125/9.011.01/source/dkms.conf)
Deprecated feature: REMAKE_INITRD (/var/lib/dkms/r8125/9.011.01/source/dkms.conf)
dkms: autoinstall for kernel: 6.2.16-4-pve.
run-parts: executing /etc/kernel/postinst.d/initramfs-tools 6.2.16-4-pve /boot/vmlinuz-6.2.16-4-pve
update-initramfs: Generating /boot/initrd.img-6.2.16-4-pve
```

有几个包是被降级的，所以之后再 apt update 时就要小心不要升级这几个包

- linux-tools-6.2-dbgsym
- pve-headers-6.2.16-4-pve
- pve-kernel-6.2.16-4-pve

> 备注: 这个方案只能是先用其他硬盘做系统盘安装好 pve 8.0, 然后打补丁，再安装 p7000z 4t 硬盘做从盘使用。

## 识别情况

打好补丁之后，p7000z 4t 硬盘就可以正常被 pve 8.0 识别了。执行

```bash
lspci
```

可以看到：

```bash
01:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
02:00.0 Non-Volatile memory controller: MAXIO Technology (Hangzhou) Ltd. NVMe SSD Controller MAP1602 (rev 01)
04:00.0 Ethernet controller: Mellanox Technologies MT27520 Family [ConnectX-3 Pro]
06:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
07:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
09:00.0 Non-Volatile memory controller: Samsung Electronics Co Ltd NVMe SSD Controller SM981/PM981/PM983
```

`02:00.0 Non-Volatile memory controller: MAXIO Technology (Hangzhou) Ltd. NVMe SSD Controller MAP1602 (rev 01)` 就是这块 p7000z 硬盘了。

执行

```bash
fdisk -l
```

可以看到硬盘信息：

```bash
Disk /dev/nvme1n1: 3.73 TiB, 4096805658624 bytes, 8001573552 sectors
Disk model: aigo NVMe SSD P7000Z 4TB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: BE883E7B-55A1-45F2-A8EF-589159CEF00D

Device           Start        End    Sectors  Size Type
/dev/nvme1n1p1      34       2047       2014 1007K BIOS boot
/dev/nvme1n1p2    2048    2099199    2097152    1G EFI System
/dev/nvme1n1p3 2099200 8001573518 7999474319  3.7T Linux LVM
```

