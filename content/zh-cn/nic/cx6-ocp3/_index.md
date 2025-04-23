---
title: "Mellanox cx6 ocp3网卡"
linkTitle: "cx6 ocp3"
weight: 9000
date: 2022-03-10
description: >
  Mellanox cx6 ocp3 200g网卡
---

曾经购入过两块cx6 200g网卡，ocp3 接口，用 ocp3转pcie4 转换卡安装到 z690 主板。结果因为 pcie 信号强度问题降速，可惜了。

记录一下当时测试的一些情况，以后买 ocp 网卡需要谨慎。

## lspci


插在 pcie 4.0 4x 槽上：

```bash
sudo lspci -s 01:00.0 -vvv | grep Width                     
[sudo] password for sky:          
		LnkCap:	Port #0, Speed 8GT/s, Width x4, ASPM not supported
		LnkSta:	Speed 8GT/s (ok), Width x4 (ok)

```

插在 pcie 4.0 16x 槽上：

```bash
mlx5_core 0000:01:00.0 enp1s0np0: Link up
➜  ~ sudo lspci -s 01:00.0 -vvv | grep Width  
		LnkCap:	Port #0, Speed 16GT/s, Width x16, ASPM not supported
		LnkSta:	Speed 8GT/s (downgraded), Width x16 (ok)
```



## dmesg

插在 pcie 4.0 4x 槽上：

```bash
sudo dmesg | grep mlx5_core
[    1.437917] mlx5_core 0000:04:00.0: enabling device (0000 -> 0002)
[    1.438118] mlx5_core 0000:04:00.0: firmware version: 20.27.446
[    1.438142] mlx5_core 0000:04:00.0: 8.000 Gb/s available PCIe bandwidth, limited by 2.5 GT/s PCIe x4 link at 0000:00:1b.4 (capable of 252.048 Gb/s with 16.0 GT/s PCIe x16 link)
[    1.731931] mlx5_core 0000:04:00.0: Rate limit: 127 rates are supported, range: 0Mbps to 97656Mbps
[    1.732211] mlx5_core 0000:04:00.0: E-Switch: Total vports 10, per vport: max uc(1024) max mc(16384)
[    1.734873] mlx5_core 0000:04:00.0: Port module event: module 0, Cable plugged
[    1.735122] mlx5_core 0000:04:00.0: mlx5_pcie_event:299:(pid 324): Detected insufficient power on the PCIe slot (25W).
[    1.741863] mlx5_core 0000:04:00.0: MLX5E: StrdRq(0) RqSz(1024) StrdSz(2048) RxCqeCmprss(1 basic)
[    1.989891] mlx5_core 0000:04:00.0 eth0: Disabling rxhash, not supported when CQE compress is active
[    1.990170] mlx5_core 0000:04:00.0 enp4s0np0: renamed from eth0
[    5.203057] mlx5_core 0000:04:00.0 enp4s0np0: Link down
```

插在 pcie 4.0 16x 槽上：

```bash
sudo dmesg | grep mlx5_core
[    1.444801] mlx5_core 0000:01:00.0: enabling device (0000 -> 0002)
[    1.445005] mlx5_core 0000:01:00.0: firmware version: 20.27.446
[    1.445026] mlx5_core 0000:01:00.0: 126.016 Gb/s available PCIe bandwidth, limited by 8.0 GT/s PCIe x16 link at 0000:00:01.0 (capable of 252.048 Gb/s with 16.0 GT/s PCIe x16 link)
[    1.684398] mlx5_core 0000:01:00.0: Rate limit: 127 rates are supported, range: 0Mbps to 97656Mbps
[    1.684600] mlx5_core 0000:01:00.0: E-Switch: Total vports 10, per vport: max uc(1024) max mc(16384)
[    1.686818] mlx5_core 0000:01:00.0: Port module event: module 0, Cable plugged
[    1.687055] mlx5_core 0000:01:00.0: mlx5_pcie_event:304:(pid 328): PCIe slot advertised sufficient power (75W).
[    1.693066] mlx5_core 0000:01:00.0: MLX5E: StrdRq(1) RqSz(8) StrdSz(2048) RxCqeCmprss(0 basic)
[    1.801740] mlx5_core 0000:01:00.0 enp1s0np0: renamed from eth0
[    5.189354] mlx5_core 0000:01:00.0 enp1s0np0: Link down
[   21.129045] mlx5_core 0000:01:00.0 enp1s0np0: Link up
```



## mst

插在 pcie 4.0 4x 槽上：

```bash
sudo mst status
MST modules:
------------
    MST PCI module is not loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4123_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:04:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
```

插在 pcie 4.0 16x 槽上：

```bash
sudo mst status
MST modules:
------------
    MST PCI module is not loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4123_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:01:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
```



## mlxlink

插在 pcie 4.0 4x 槽上：

```bash
sudo mlxlink -d /dev/mst/mt4123_pciconf0 --port_type PCIE -e

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 0, 0
Link Speed Active (Enabled)        : 2.5G-Gen 1 (16G-Gen 4)
Link Width Active (Enabled)        : 4X (16X)
 

Errors
------
Showing Eye via SLRG raised the following exception: Eye information available for Gen3 and above
```

降速严重。



插在 pcie 4.0 16x 槽上：

```bash
sudo mlxlink -d /dev/mst/mt4123_pciconf0 --port_type PCIE -e

PCIe Operational (Enabled) Info
-------------------------------
Depth, pcie index, node            : 0, 0, 0
Link Speed Active (Enabled)        : 8G-Gen 3 (16G-Gen 4)
Link Width Active (Enabled)        : 16X (16X)

EYE Opening Info (PCIe)
-----------------------
Physical Grade                     :   1848,  2220,  2146,  2301,  2067,  2052,  1944,  2135,  2035,  1980,  2090,  2340,  1980,  2170,  2196,  2072
Height Eye Opening [mV]            :    147,   177,   171,   184,   165,   164,   155,   170,   162,   158,   167,   187,   158,   173,   175,   165
Phase  Eye Opening [psec]          :     16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16,    16


```



## 设置 mtu



```bash
sudo ifconfig enp1s0np0 mtu 9000 up
```

