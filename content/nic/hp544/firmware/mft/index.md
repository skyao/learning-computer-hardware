---
title: "查看固件"
linkTitle: "查看固件"
weight: 10
date: 2022-03-10
description: >
  介绍MFT工具软件的安装和使用，查看当前固件信息
---


## 介绍

MFT 是 Nvidia Firmware Tools （Mellanox Firmware Tools），包含多个工具，日常主要用到的是：

1. mst

   该工具提供以下功能：

   * 启动/停止寄存器访问驱动程序
   * 列举可用的mst设备

2. flint

   该工具可将固件二进制镜像或扩展ROM镜像刻录到NVIDIA网络适配器/网关/交换机设备的闪存设备中。它包括对刻录的固件镜像和二进制镜像文件的查询功能。


## 下载

下载地址： [NVIDIA Firmware Tools (MFT)](https://network.nvidia.com/products/adapter-software/firmware-tools/)

在页面中的 **MFT Download Center** 下找到对应的平台，一般常用的就是 windows 和 linux：

- https://www.mellanox.com/downloads/MFT/WinMFT_x64_4_24_0_72.exe 
- https://www.mellanox.com/downloads/MFT/mft-4.24.0-72-x86_64-deb.tgz

## 安装

### windows安装

直接打开下载的 WinMFT_x64_4_24_0_72.exe 安装即可，默认安装路径为 `C:\Program Files\Mellanox\WinMFT`

### linux安装

以 ubuntu 为例，

```bash
$ wget --no-check-certificate https://www.mellanox.com/downloads/MFT/mft-4.24.0-72-x86_64-deb.tgz
$ tar xvf mft-4.24.0-72-x86_64-deb.tgz
$ cd mft-4.24.0-72-x86_64-deb
# 如果缺少依赖包会导致安装失败，可以先apt命令安装以下包
$ sudo apt-get install gcc make dkms
$ sudo ./install.sh
-I- Removing mft external packages installed on the machine
-I- Installing package: /home/sky/hp544/mft-4.24.0-72-x86_64-deb/SDEBS/kernel-mft-dkms_4.24.0-72_all.deb
-I- Installing package: /home/sky/hp544/mft-4.24.0-72-x86_64-deb/DEBS/mft_4.24.0-72_amd64.deb
-I- In order to start mst, please run "mst start".
```

## 启动

在 linux 下需要运行如下命令启动 mst （windows 下不需要）：

```bash
sudo mst start

Starting MST (Mellanox Software Tools) driver set
Loading MST PCI module - Success
Loading MST PCI configuration module - Success
Create devices
```

## 使用

所有工具都有权限要求，在 windows 下打开 cmd 时必须选择以管理员身份运行 cmd ，linux下需要用到 sudo。

### mst工具

`mst status` 用来查看当前的网卡情况，linux下是这样：

```bash
sudo mst status
MST modules:
------------
    MST PCI module loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4103_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:04:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
/dev/mst/mt4103_pciconf1         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:14:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
/dev/mst/mt4103_pciconf2         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:1d:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
/dev/mst/mt4103_pci_cr0          - PCI direct access.
                                   domain:bus:dev.fn=0000:04:00.0 bar=0xfe700000 size=0x100000
                                   Chip revision is: 00
/dev/mst/mt4103_pci_cr1          - PCI direct access.
                                   domain:bus:dev.fn=0000:14:00.0 bar=0xfd900000 size=0x100000
                                   Chip revision is: 00
/dev/mst/mt4103_pci_cr2          - PCI direct access.
                                   domain:bus:dev.fn=0000:1d:00.0 bar=0xfd100000 size=0x100000
                                   Chip revision is: 00
```

我这里插了三块卡，所以出来的信息比较多，如果只有一块网卡，则应该是这样：

```bash
sudo mst status
MST modules:
------------
    MST PCI module loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4103_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:04:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
                                   Chip revision is: 00
/dev/mst/mt4103_pci_cr0          - PCI direct access.
                                   domain:bus:dev.fn=0000:04:00.0 
```

windows下输出内容类似，只是 devices 信息中，"/dev/mst/mt4103_pci_cr0" 会变成 "mt4103_pci_cr0"。

```bash
mst status

MST devices:
------------
  mt4103_pci_cr0
  mt4103_pciconf0
```

### mlxfwmanager工具

mlxfwmanager工具可以用来查询当前固件信息：

```bash
sudo mlxfwmanager
Querying Mellanox devices firmware ...

Device #1:
----------

  Device Type:      ConnectX3Pro
  Part Number:      764285-B21_Ax
  Description:      HP InfiniBand FDR/Ethernet 10Gb/40Gb 2-port 544+FLR-QSFP Adapter
  PSID:             HP_1380110017
  PCI Device Name:  /dev/mst/mt4103_pci_cr2
  Port1 MAC:        e0071b783ea1
  Port2 MAC:        e0071b783ea2
  Versions:         Current        Available     
     FW             2.42.5700      N/A           
     CLP            8025           N/A           
     PXE            3.4.0754       N/A           
     UEFI           14.11.0049     N/A           

  Status:           No matching image found
```

`FW             2.42.5700      N/A` 这里显示的就是当前设置的固件版本。5700是最新的固件版本，有些老一点的固件会显示为类似 `     FW             2.42.5016      N/A`。

注意：在 linux 下运行时要 sudo，不然没有权限会显示没有设备的信息，容易被误导：

```bash
mlxfwmanager  
-E- No devices found or specified, mst might be stopped, run 'mst start' to load MST modules
➜  mft-4.24.0-72-x86_64-deb sudo mlxfwmanager
Querying Mellanox devices firmware ...
```

类似的，在 windows 下打开 cmd 时必须选择以管理员身份运行 cmd 。

### flint工具

flint工具用于刷新网卡的固件，后续详细介绍。
