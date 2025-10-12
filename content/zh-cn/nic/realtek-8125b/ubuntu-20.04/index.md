---
title: "ubuntu 20.04 驱动安装"
linkTitle: "[归档]ubuntu 20.04"
weight: 100
date: 2022-03-10
description: >
  在 ubuntu 20.04 下安装 Realtek 8125b 驱动
---

{{% alert title="归档说明" %}} 

可能当时用到的这个 r8125 驱动还不够成熟？也许换到2025年新发的版本就解决了？但因为我后来因为升级不再使用 ubuntu 20.04 这个版本了。就不再继续研究。

{{% /alert %}}

------------------

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

## 掉驱动的问题

使用中发现偶尔会出现一个问题：在某一次重启后，安装好的r8125驱动消失了，r8139重新出现。然后由于 r8139 无法驱动 Realtek 8125b 网卡，因此导致这个网卡不可用。

这个问题基本上每隔一两个月就会发生一次。解决的方式也简单，重新再安装一次 r8125 驱动即可。但是如果遇到人在远程就束手无策了。

google了一下，发现这里有人遇到过和我类似的问题：

https://askubuntu.com/questions/1259947/cant-get-rtl8125b-working-on-20-04

> After installing r8125, from time to time my PC automatically upgrades the driver to r8169, making internet inaccessible again, do you guys know how to disable this specific upgrade?

这个问题也可以通过升级到高一点版本的内核即可解决，比如 5.10 以上。但由于 hp544+ 网卡驱动的问题，我有些电脑不得不停留在 5.4 内核，因此这个问题总是会偶尔发生一次。

这里也讨论到类似问题：

https://ubuntu-mate.community/t/realtek-rtl8125-2-5gbe-ethernet-not-working-on-amd-b550-mobo/22469/4

有意见说是每当内核更新时，需要重新安装这个驱动。

> After each kernel update , you must re install ethernet driver .
> 
> So for removing :
> 
> sudo modprobe -rfv r8125
>
> sudo dkms remove -m r8125 -v 9.003.05 --all
>
> sudo rm -r /usr/src/r8125-9.003.05
>
> then reinstall with the initial procedure seen before .

```bash
ls /usr/src/
kernel-mft-dkms-4.24.0   linux-headers-5.4.0-169-generic  linux-headers-5.4.0-170-generic
linux-headers-5.4.0-169  linux-headers-5.4.0-170
```
