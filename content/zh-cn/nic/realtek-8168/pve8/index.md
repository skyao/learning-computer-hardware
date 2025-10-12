---
title: "PVE 8 安装驱动"
linkTitle: "PVE 8"
weight: 20
date: 2025-06-21
description: >
  在 pve8 下为 Realtek 8168 千兆网卡安装驱动
---

## 问题表现

pve8.2 下，默认为 RTL8111 和 RTL8168H 网卡(也包括 RTL8125b 网卡)都采用 r8169 驱动，但是 r8169 驱动无法驱动 RTL8168H 网卡（可以驱动 RTL8111 网卡和 RTL8125b 网卡）。

我遇到的问题是主板板载 realtek 千兆网卡，全新安装 pve8.2，在安装过程中无法识别到板载的 RTL8168H 网卡，尤其没有插其他网卡的情况下，安装程序会提示没有可用网卡而无法继续安装。

我在主板上多插了一块 RTL8125b 网卡，完成 pve8.2 的安装后，继续检查， 发现 lspci 可以看到这两块网卡：

```bash
 lspci | grep Real
01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 2b)
```

但是，`ip addr` 只能看到一块网卡：

```bash
ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master vmbr0 state UP group default qlen 1000
    link/ether 38:ca:73:fe:01:5f brd ff:ff:ff:ff:ff:ff
```

## 排查问题

查看内核模块, 可以看到只有 r8169 模块：

```bash
lsmod | grep r81
r8169                 110592  0
```

执行命令：

```bash
lspci -k
```

可以看到两个网卡的驱动情况:

```bash
01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
        Subsystem: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller
        Kernel driver in use: r8169
        Kernel modules: r8169
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 2b)
        Subsystem: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller
        Kernel modules: r8169
```

两个网卡都使用 r8169 模块， 但是 r8169 模块无法驱动 RTL8111/8168/8411 网卡， RTL8125 网卡则可以驱动（体现为 Kernel driver in use: r8169 这一行）。

按照以往的经验，r8169 模块是可以驱动 RTL8111 网卡的， 毕竟 RTL8111 网卡是一块大量使用的板载网卡的，之前都用过没有出现类似问题。

通过检查 dmesg 日志：

```bash
dmesg | grep -i r8169
```

可以发现如下信息：

```bash
[    2.526047] r8169 0000:02:00.0: error -ENODEV: unknown chip XID 6c0, contact r8169 maintainers (see MAINTAINERS file)
```

日志清晰的说明了是 r8169 模块无法驱动这块板载的 RTL8111/8168/8411 网卡。注意到信息中的 `unknown chip XID 6c0` 字样，经过查阅资料，发现这是采用了新芯片的 RTL8168H 网卡。而 r8169 模块无法驱动这块 RTL8168H 网卡。

## 安装 R8168 驱动

为了驱动 RTL8168H 网卡，需要安装 r8168 驱动。

r8168 驱动的 github 地址：

https://github.com/mtorromeo/r8168

下载地址为：

https://www.realtek.com/Download/List?cate_id=584

找到 Linux 下的 "GBE Ethernet LINUX driver r8168 for kernel up to 6.12", 下载 8.055.00 版本, 得到 r8168-8.055.00.tar.bz2 文件。

将文件传到服务器上，解压后进入目录, 准备执行 autorun.sh 脚本：

```bash
tar -xvf r8168-8.055.00.tar.bz2
cd r8168-8.055.00

# 谨慎：准备好之后再执行安装
#./autorun.sh 
```

但特别注意：一定要先安装 pve headers 包：

```bash
apt install pve-headers-$(uname -r)
```

否则安装驱动时会报错退出，而 r8168 驱动的过程中会先删除旧的版本（也就是 r8169 ），然后再安装 r8168 驱动。因此最好不要远程安装，否则 r8169 被卸载时会断开连接。如果中间 r8168 驱动安装失败，比如没有安装 pve headers 包，就会无法再连接这个机器。好在重启之后，会默认继续加载 r8169 驱动。

确认安装好 pve headers 包后，执行 autorun.sh：

命令输出为：

```bash
Check old driver and unload it.
rmmod r8169
......
```

完成后重启，之后查看内核模块，发现 r8169 模块已经被卸载，r8168 模块已经加载：

```bash
lsmod | grep r81
r8168                 667648  0
```

可以看到 r8168 模块已经加载。`ip addr` 可以看到 enp2s0 网卡已经识别到：

```bash
......
2: enp2s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 00:e2:69:97:cd:46 brd ff:ff:ff:ff:ff:ff
......
```

可以看到 enp2s0 这块 rtl8168h 网卡已经识别到。`lspci -k` 可以看到 r8168 驱动已经加载：

```bash
$ lspci -k

......
02:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller (rev 2b)
        Subsystem: Realtek Semiconductor Co., Ltd. RTL8111/8168/8211/8411 PCI Express Gigabit Ethernet Controller
        Kernel driver in use: r8168
        Kernel modules: r8168
```

## 补充

安装 r8168 驱动后，发现 r8125 网卡无法正常工作，这是因为 r8168 驱动安装时卸载了原来的 r8169 驱动。 但 r8169 可以驱动 r8125 网卡，而新安装的 r8168 不能驱动 r8125 网卡。

这就造成了 r8125 网卡因为没有驱动无法正常工作。

解决办法是为 r8125 网卡安装它自己的驱动，方法类似，在上面的下载页面可以找到 "2.5G Ethernet LINUX driver r8125 for kernel up to 6.12"。

而且，下载页面也不再提供新版本的 r8169 驱动，因此，如果要同时使用 r8125 2.5g 网卡和 r8111 / r8168 千兆网卡，最好的方法就是不使用 r8169 驱动，而是分别为他们安装 r8125 和 r8168 驱动。比较遗憾的是，pve8.2 目前的默认驱动是 r8169，因此在某些情况下会造成很大的麻烦，比如我就被折腾了一圈。

## 总结

总结一下这几个网卡和驱动的支持情况：

|        驱动\网卡         | realtek 8111 千兆网卡 | realtek 8168 千兆网卡 | realtek 8125 2.5g网卡 |
| --------------- | --------------------- | --------------------- | --------------------- |
| 默认 r8169 驱动 | 支持                  | 不支持                | 支持                  |
| r8168 驱动      | 支持                  | 支持                  | 不支持                |
| r8125 驱动      | 不支持                | 不支持                | 支持                  |









