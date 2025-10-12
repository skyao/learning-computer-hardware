---
title: "PVE 8 驱动安装"
linkTitle: "PVE 8"
weight: 20
date: 2025-06-21
description: >
  在 pve8 下为 Realtek 8125b 2.5g 网卡安装驱动
---

## 驱动说明

默认情况下，pve8 使用 r8169 驱动，可以驱动 8125b 2.5g 网卡，因此不需要安装额外的驱动就可以直接使用 r8125b 网卡。

需要单独安装 r8125 驱动的情况有：

1. 性能

   r8169 驱动没有开启网卡多队列等特性，会导致一个网卡的软中断只能使用固定的一个核心开接收和发送数据。

   参考文章：https://zhuanlan.zhihu.com/p/650244240

2. 驱动冲突

   如果需要同时使用 r8125b 和 r8168 网卡，则需要安装 r8125 驱动。

   参考后面为 r8168 网卡安装 r8168 驱动的章节。

## 安装驱动

### deb 安装

参考文章：

- https://forum.proxmox.com/threads/rtl8125-2-5gbe-nic-not-working-pve-8-2-7-kernel-6-8-12-2-pve.155637/

从下面的地址下载到 r8125 驱动的最新版本：

https://github.com/awesometic/realtek-r8125-dkms/releases

我当前下载的是 realtek-r8125-dkms_9.015.00-1_amd64.deb。

以下是远程 ssh 上去之后进行安装的步骤：

```bash
# 确保安装了 pve-headers 包
apt install pve-headers-$(uname -r)

# 下载 r8125 驱动
wget https://github.com/awesometic/realtek-r8125-dkms/releases/download/9.015.00-1/realtek-r8125-dkms_9.015.00-1_amd64.deb

# 安装 r8125 驱动
dpkg -i realtek-r8125-dkms_9.015.00-1_amd64.deb

# 重启机器
reboot
```

查看网卡驱动信息：

```bash
$ lspci  -k
# 更精准一些
$ lspci -k -s 01:00.0
......
01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
        Subsystem: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller
        Kernel driver in use: r8169
        Kernel modules: r8169, r8125

```

r8169, r8125 两个模块都存在，说明驱动安装成功。但网卡使用的还是 r8169 驱动。

卸载 r8169 驱动，加载 r8125 驱动，重启网卡，这三个命令必须同时送到服务器端执行， 因为删除 r8169 之后网络就会断开：

```bash
# 创建黑名单配置文件
echo "blacklist r8169" | sudo tee /etc/modprobe.d/blacklist-r8169.conf

# 禁止内核自动加载 r8169
echo "install r8169 /bin/false" | sudo tee -a /etc/modprobe.d/blacklist-r8169.conf

# 创建驱动优先级配置
echo "options r8125 debug=1" | sudo tee /etc/modprobe.d/r8125.conf

# 更新 initramfs
update-initramfs -u

# 重启机器
reboot
```

重启之后查看网卡驱动信息：

```bash
$ lspci -k -s 01:00.0

01:00.0 Ethernet controller: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller (rev 05)
        Subsystem: Realtek Semiconductor Co., Ltd. RTL8125 2.5GbE Controller
        Kernel driver in use: r8125
        Kernel modules: r8169, r8125

$ lsmod | grep r81
r8125                 299008  0
```

可以看到 r8125 驱动已经加载成功, r8169 驱动已经被禁用。

