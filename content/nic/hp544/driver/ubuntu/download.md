---
title: "下载linux驱动"
linkTitle: "下载驱动"
weight: 10
date: 2022-03-10
description: >
  下载hp544+网卡的linux驱动
---



## 下载驱动

下载页面：

[Linux InfiniBand Drivers (nvidia.com)](https://network.nvidia.com/products/infiniband-drivers/linux/mlnx_ofed/)

按照页面的提示：

**Note:** MLNX_OFED 4.9-x LTS should be used by customers who would like to utilize one of the following:

- NVIDIA ConnectX-3 Pro
- NVIDIA ConnectX-3
- NVIDIA Connect-IB
- RDMA experimental verbs library (mlnx_lib)
- OSs based on kernel version lower than 3.10

**Note:** All of the above are not available on MLNX_OFED 5.x branch.



hp544+网卡属于 ConnectX-3 Pro，因此只能下载 **4.9** 驱动。



windows驱动目前有 53000 和 54000 两个版本，其中 54000 是 Windows Server 2019 专用的，其他操作系统请使用 53000

> WinOF v5.50.54000 includes a driver for Windows Server 2019 only. For other OSes, please see WinOF v5.50.53000.

下载链接：

- Ubuntu 20.04 : [MLNX_OFED_LINUX-4.9-6.0.6.0-ubuntu20.04-x86_64.tgz](https://www.mellanox.com/page/mlnx_ofed_eula?mtag=linux_sw_drivers&mrequest=downloads&mtype=ofed&mver=MLNX_OFED-4.9-6.0.6.0&mname=MLNX_OFED_LINUX-4.9-6.0.6.0-ubuntu20.04-x86_64.tgz)





