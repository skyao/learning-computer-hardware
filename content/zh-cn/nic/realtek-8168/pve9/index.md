---
title: "PVE 9 驱动情况"
linkTitle: "PVE 9"
weight: 30
date: 2025-11-09
description: >
  pve0 自带 Realtek 8168 千兆网卡驱动
---

pve9 下之前的问题不复存在，Realtek 8168 千兆网卡自动就可以功做。

### 设备情况

```bash
$ lspci | grep Real

02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller (rev 2b)
```

默认直接可以使用：

```bash
$ ip addr

2: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master vmbr0 state UP group default qlen 1000
    link/ether 00:e2:69:97:cd:46 brd ff:ff:ff:ff:ff:ff
    altname enx00e26997cd46
```

不需要在 pve8 下那么折腾。

### 驱动情况

看了一下 pve9 下的驱动, 默认使用的是 r8169 mod：

```bash
$ lsmod | grep r81

r8169                 122880  0

$ lspci -k

......
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller (rev 2b)
        Subsystem: Realtek Semiconductor Co., Ltd. Device 0123
        Kernel driver in use: r8169
        Kernel modules: r8169

```

r8169 在 pve8 下无法驱动 realtek 8168 千兆网卡，逼得我不得不单独安装 r8168 mod。但在 pve9 下的这个 r8169 mod 已经可以驱动 realtek 8168 千兆网卡了。不知道是不是 pve9 自带的 r8169 mod 已经更新了？

系统启动日志中，可以看到 r8169 mod 已经可以驱动 realtek 8168 千兆网卡了：

```bash
$ dmesg | grep -i r8169

[    0.806467] r8169 0000:02:00.0: can't disable ASPM; OS doesn't have ASPM control
[    0.811827] r8169 0000:02:00.0 eth0: RTL8168h/8111h, 00:e2:69:97:cd:46, XID 6c0, IRQ 134
[    0.811830] r8169 0000:02:00.0 eth0: jumbo features [frames: 9194 bytes, tx checksumming: ko]
[    1.128963] r8169 0000:02:00.0 enp2s0: renamed from eth0
[    2.981308] r8169 0000:02:00.0 enp2s0: entered allmulticast mode
[    2.981353] r8169 0000:02:00.0 enp2s0: entered promiscuous mode
[    3.005423] Generic FE-GE Realtek PHY r8169-0-200:00: attached PHY driver (mii_bus:phy_addr=r8169-0-200:00, irq=MAC)
[    3.123685] r8169 0000:02:00.0 enp2s0: Link is Down
[   67.869569] r8169 0000:02:00.0 enp2s0: Link is Up - 1Gbps/Full - flow control rx/tx
```






