---
title: "介绍"
linkTitle: "介绍"
weight: 10
date: 2025-06-21
description: >
  Realtek 8168 千兆网卡介绍
---

## 介绍

Realtek 8168 千兆网卡, 和 Realtek 8111 千兆网卡非常类似。

但是 RTL8168 的新型号如 RTL8168H，驱动和 RTL8111 不兼容，某些时候会造成问题，因此单独列出来处理。

### 如何识别

如何识别当前的网卡是 RTL8111 还是 RTL8168H？毕竟两者的信息非常类似，下面是在 linux 下用 lspci 命令的输出，这是一块 RTL8111 的网卡：

```bash
$ lspci | grep Real

04:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 11)

$ lspci -nn -s 04:00.0
04:00.0 Ethernet controller [0200]: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller [10ec:8168] (rev 11)
```

下面是一块 RTL8168H 的网卡：

```bash
$ lspci | grep Real

02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller (rev 2b)

$ lspci -nn -s 02:00.0
02:00.0 Ethernet controller [0200]: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller [10ec:8168] (rev 2b)
```

这两个网卡芯片的PCI 设备 ID 都是 10ec:8168，表示芯片属于 RTL8168 系列（RTL8111 和 RTL8168 共用此 ID，需进一步区分）。上面的信息中仅仅在 rev 字段有区别，RTL8111 的 rev 是 11，RTL8168H 的 rev 是 2b。

在 linux 下，realtek 的驱动 r8169 可以驱动 RTL8111 ，但是无法驱动 RTL8168H，体现为 rRTL8168H 网卡无法加载驱动，无法被识别。 即 lspci 可以看到网卡，但是 ip addr 无法看到。

通过检查 dmesg 日志：

```bash
dmesg | grep -i r8169
```

可以发现如下信息：

```bash
[    2.526047] r8169 0000:02:00.0: error -ENODEV: unknown chip XID 6c0, contact r8169 maintainers (see MAINTAINERS file)
```

到这里就可以清晰的判断出来是 RTL8168H 网卡了：

- PCI ID：`10ec:8168`
- Revision：`2b`, 版本字段，Rev
- r8169 驱动报错：`unknown chip XID 6c0`

