---
title: "openwrt固件"
linkTitle: "openwrt"
weight: 10
date: 2026-07-01
description: >
  TP-link XDR6088 刷机 openwrt 固件
---

## 特别说明

网口损坏后送修，修好之后固件被更新为官方最新版本，我懒的自己折腾刷固件，就找人帮忙刷了 openwrt。

但刷的固件版本似乎很特殊：

```bash
cat /etc/openwrt_release
DISTRIB_ID='OpenWrt'
DISTRIB_RELEASE='24.10.5'
DISTRIB_TARGET='mediatek/filogic'
DISTRIB_ARCH='aarch64_cortex-a53'
DISTRIB_TAINTS='no-all'
DISTRIB_REVISION='R26.02.20'
DISTRIB_DESCRIPTION='LEDE '

uname -a
Linux OpenWrt 6.12.74 #0 SMP Thu Mar  5 10:20:00 2026 aarch64 GNU/Linux
```

这个固件以 OpenWrt 24.10.5 为基础，但移植了更新的 Linux 内核 (6.12.74)，同时界面 (LuCI) 却来自较旧的 23.05 分支。这是一个典型的“自编译混合固件”。不管了，就这么用到烂吧。

## 基本配置

### opkg 配置

修改 `/etc/opkg/distfeeds.conf` 文件或者登录后在 系统设置 -》 插件管理 -》 配置 opkg 中设置：

```bash
src/gz openwrt_core https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/targets/mediatek/filogic/packages
src/gz openwrt_base https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/packages/aarch64_cortex-a53/base
src/gz openwrt_luci https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/packages/aarch64_cortex-a53/luci
src/gz openwrt_packages https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/packages/aarch64_cortex-a53/packages
src/gz openwrt_routing https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/packages/aarch64_cortex-a53/routing
src/gz openwrt_telephony https://mirrors.tencent.com/lede/releases/23.05-SNAPSHOT/packages/aarch64_cortex-a53/telephony

#src/gz kenzo https://github.com/kenzok8/small-package/releases/download/23.05/kenzo
#src/gz small https://github.com/kenzok8/small-package/releases/download/23.05/small

src/gz kenzo https://ghproxy.com/github.com/kenzok8/small-package/releases/download/23.05/kenzo
src/gz small https://ghproxy.com/github.com/kenzok8/small-package/releases/download/23.05/small
```

## 安装插件

### openclash

从如下地址下载 openwrt 的 ipk 安装包，如 luci-app-openclash_0.47.110_all.ipk：

https://github.com/vernesong/OpenClash/releases

然后在页面上通过上传软件包的功能上传并安装，比较奇怪的是我遇到报错。

然后我 ssh 登录之后，用 opkg 命令安装：

```bash
opkg install luci-app-openclash
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libgcc1 and libgcc1) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libpthread and libpthread) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (libpthread and libpthread) providing same name marked HOLD or PREFER. Using latest.
Multiple packages (librt and librt) providing same name marked HOLD or PREFER. Using latest.
Package luci-app-openclash (0.47.110) installed in root is up to date.
```

却又显示已经安装。不管了，重启之后发现 openclash 已经正常可以用了，版本也是最新的 0.47.110。

### zerotier

固件自带 zerotier

### 腾讯云ddns

正常安装：

http://openwrt-tencentddns-1301800460.cos.ap-guangzhou.myqcloud.com/luci-app-tencentddns_0.1.0-1_all.ipk

### 上网时间管理

固件自带。

