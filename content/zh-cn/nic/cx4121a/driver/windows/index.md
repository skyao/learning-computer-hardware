---
title: "windows 驱动"
linkTitle: "windows"
weight: 30
date: 2022-03-10
description: >
  在 windows 上安装 Mellanox MCX4121A 网卡的驱动
---


经过验证，适用于 windows 10 和 windows server 2022。

## 物理机安装

下载地址： https://network.nvidia.com/products/adapter-software/ethernet/windows/winof-2/ 

直接安装即可。

## 虚拟机安装

在虚拟机下安装驱动，如果只有一个网卡则会遇到死结：没有网卡驱动就无法从网上下载驱动，因此无法安装网卡驱动。

解决的方案是提前准备一个包含驱动的 iso 文件，然后以 cd 载入 iso 文件的方式将驱动文件传入虚拟机。

各个操作系统下制作 iso 文件的方式如下。 

### macos 下制作iso

参考：

- [macOS: Create an ISO From a Folder](https://www.petenetlive.com/KB/Article/0001554)

步骤如下：

- 将驱动文件放在一个文件夹下

- 用系统自带的 **Disk Utility** 创建一个装载该驱动文件所在目录的镜像文件， image format 选 `DVD/CD master` 格式，得到 cdr 文件

- 将 cdr 文件转为 iso 文件

  ```bash
  hdiutil makehybrid -iso -joliet -o yourname.iso yourname.cdr
  ```

- 上传 iso 文件到 pve 下。
