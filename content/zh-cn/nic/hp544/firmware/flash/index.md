---
title: "刷新固件"
linkTitle: "刷新固件"
weight: 30
date: 2022-03-10
description: >
  介绍如何刷新hp544+网卡的固件
---



## 准备工作

首先安装前面的要求安装好 MFT 工具，并下载好需要进行刷新的固件文件。

## 刷新固件

MFT 中的 flint工具用于刷新网卡的固件。

> 注意： 有权限要求，在 windows 下打开 cmd 时必须选择以管理员身份运行 cmd ，linux下需要用到 sudo。

### windows刷新固件

windows 下可以将要刷新的固件文件(如 `fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin` ) 放在mft的安装目录下，如 `C:\Program Files\Mellanox\WinMFT`。

执行 flint 命令刷新固件：

```bash
cd C:\Program Files\Mellanox\WinMFT

flint -d mt4103_pci_cr0 -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin b

    Current FW version on flash:  2.42.5016
    New FW version:               2.42.5700

Burning FS2 FW image without signatures - OK
Restoring signature                     - OK
```

再次执行 mlxfwmanager 命令查看刷新之后的固件信息：

```
mlxfwmanager
Querying Mellanox devices firmware ...

Device #1:
----------

  Device Type:      ConnectX3Pro
  Part Number:      764285-B21_Ax
  Description:      HP InfiniBand FDR/Ethernet 10Gb/40Gb 2-port 544+FLR-QSFP Adapter
  PSID:             HP_1380110017
  PCI Device Name:  mt4103_pci_cr0
  Port1 GUID:       24be05ffffbd0801
  Port2 GUID:       24be05ffffbd0802
  Versions:         Current        Available
     FW             2.42.5700      2.42.5700
     CLP            8025           8025
     FW (Running)   2.42.5016      N/A
     PXE            3.4.0754       3.4.0754
     UEFI           14.11.0049     14.11.0049

  Status:           Up to date
```

可以看到固件已经被刷新到 2.42.5700 版本。

### linux下刷新固件

linux 下类似，注意 -d 参数后面的设置信息需要是类似 "/dev/mst/mt4103_pci_cr0" 这样，具体参见 mft status 命令的输出：

```bash
sudo flint -d /dev/mst/mt4103_pci_cr0 -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin b
```

## 重置设置

刷新固件之后，推荐重置网卡设置。windows下:

```bash
mlxconfig -d mt4103_pci_cr0 reset
```

linux下:

```bash
sudo mlxconfig -d /dev/mst/mt4103_pci_cr0 reset
```

## 切换Ethernet模式

hp544+支持 ib / eth / vpi 三种模式，但一般我固定用 eth 模式（ib模式暂时还不会设置软路由）。这种情况下可以考虑将网卡的工作模式固定为 eth。

windows下:

```bash
mlxconfig -d mt4103_pci_cr0 set LINK_TYPE_P1=2  # 1. ib模式 2. eth模式 3. vpi 模式 
mlxconfig -d mt4103_pci_cr0 set LINK_TYPE_P2=2
```

linux下：

```bash
sudo mlxconfig -d /dev/mst/mt4103_pciconf0 set LINK_TYPE_P1=2
sudo mlxconfig -d /dev/mst/mt4103_pciconf0 set LINK_TYPE_P2=2
```

## 移除 flexboot

flexboot 支持网卡启动，但目前我没有这方面的需求，而 flexboot 会影响开机启动速度，因此在不需要 flexboot 的情况下可以通过移除 flexboot 来加快开机启动速度。

windows下:

```bash
flint -d mt4103_pci_cr0 --allow_rom_change drom
```

linux下：

```bash
sudo flint -d /dev/mst/mt4103_pciconf0 --allow_rom_change drom
```

