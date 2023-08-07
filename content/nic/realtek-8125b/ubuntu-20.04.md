---
title: "ubuntu 20.04 驱动安装"
linkTitle: "ubuntu 20.04"
weight: 10
date: 2022-03-10
description: >
  在 ubuntu 20.04 下安装 Realtek 8125b 驱动
---



## 问题

在 ubuntu 20.04 下，Realtek 8125b 网卡无法被识别。

lspci 可以发现该网卡，但 lsmod 只能看到 r8169 模块，而这个模块在启动时会报错。通过执行

```bash
dmesg | grep r8139
```

可以看到这样的日志信息：

```bash
r8169 0000:06:00.0: unknown chip XID 641
```

这个报错是表明 r8139 模块无法驱动 Realtek 8125b 网卡。需要安装 Realtek 8125b 的驱动。

## 安装驱动

Realtek 8125b 网卡驱动的下载地址：

https://www.realtek.com/zh-tw/component/zoo/category/network-interface-controllers-10-100-1000m-gigabit-ethernet-pci-express-software

在 "Unix (Linux)" 下找到 "2.5G Ethernet LINUX driver r8125 for kernel up to 5.19" ，下载下来，然后解压缩并复制到 ubuntu 20.04 下。

然后执行

```bash
cd r8125-9.011.01
sudo ./autorun.sh
```

驱动安装完毕后重启即可。
