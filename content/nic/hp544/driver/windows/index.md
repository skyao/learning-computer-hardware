---
title: "windows驱动"
linkTitle: "windows"
weight: 10
date: 2022-03-10
description: >
  下载安装hp544+网卡的windows驱动
---



## 下载

下载页面：

[Mellanox OFED for Windows - WinOF / WinOF-2 (nvidia.com)](https://network.nvidia.com/products/adapter-software/ethernet/windows/winof-2/)

按照页面的提示：

For **ConnectX-3** and **ConnectX-3 Pro** drivers download **WinOF.**

For **ConnectX-4 and onwards adapter cards** drivers download **WinOF-2.**

hp544+网卡属于 ConnectX-3 Pro，因此只能下载 **WinOF.** 驱动。

windows驱动目前有 53000 和 54000 两个版本，其中 54000 是 Windows Server 2019 专用的，其他操作系统请使用 53000

> WinOF v5.50.54000 includes a driver for Windows Server 2019 only. For other OSes, please see WinOF v5.50.53000.

下载链接：

- windows10: [MLNX_VPI_WinOF-5_50_53000_All_Win2019_x64.exe](https://www.mellanox.com/downloads/WinOF/MLNX_VPI_WinOF-5_50_53000_All_Win2019_x64.exe)
- windows server 2019: [MLNX_VPI_WinOF-5_50_54000_All_win2019_x64.exe](https://www.mellanox.com/downloads/WinOF/MLNX_VPI_WinOF-5_50_54000_All_win2019_x64.exe)
- win8.1: [MLNX_VPI_WinOF-5_50_53000_All_Win2012R2_x64.exe](https://www.mellanox.com/downloads/WinOF/MLNX_VPI_WinOF-5_50_53000_All_Win2012R2_x64.exe)



## 安装

### windows 10 安装

直接运行下载的 MLNX_VPI_WinOF-5_50_53000_All_Win2019_x64.exe 文件即可。

