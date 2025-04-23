---
title: "华为sp350刷新固件"
linkTitle: "固件"
weight: 20
date: 2022-03-10
description: >
  给华为sp350网卡刷新固件
---

目前华为sp350网卡无法刷新官方固件，暂时放弃。

## 附录

### debian 12 下安装 mft

Debian 12 下安装 mft 的方式参考 cx4121a 一节，方法是类似的。

但是，不知道为什么安装完成后无法启动 

```bash
$ sudo mst start                       
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI modulemodprobe: ERROR: could not insert 'mst_pci': Key was rejected by service
 - Failure: 1
Loading MST PCI configuration modulemodprobe: ERROR: could not insert 'mst_pciconf': Key was rejected by service
 - Failure: 1
Create devices

mst_pci driver not found
Unloading MST PCI module (unused) - Success
Unloading MST PCI configuration module (unused) - Success
```

反复测试过：

- 不管是用 debian12 自带的驱动，还是安装官方最新驱动，mst 都无法启动。

- mft mft-4.26 和 mft-4.27 两个版本都测试过，一样的问题

### windows10 下删除 rom

为了开机更快一些，在 windows 下删除网卡的 rom，方法是通用的：

```bash
flint -d /dev/mst/mt4117_pciconf0 --allow_rom_change drom
```

执行过程很慢：

```bash
-I- Preparing to remove ROM ...
Removing ROM image    - OK  # 这一步要1-2分钟
Restoring signature  - OK
```

