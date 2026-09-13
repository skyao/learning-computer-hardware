---
title: "debian13"
linkTitle: "debian13"
weight: 10
date: 2026-05-31
description: p102显卡在 debian13 物理机上安装
---

## 准备工作

修改源文件：

```bash
sudo vi /etc/apt/sources.list
```

主要是每行的末尾设置为"main contrib non-free non-free-firmware"：

```bash
deb http://mirrors.ustc.edu.cn/debian/ trixie main contrib non-free non-free-firmware
deb-src http://mirrors.ustc.edu.cn/debian/ trixie main contrib non-free non-free-firmware

deb http://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware
deb-src http://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware

# trixie-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://mirrors.ustc.edu.cn/debian/ trixie-updates main contrib non-free non-free-firmware
deb-src http://mirrors.ustc.edu.cn/debian/ trixie-updates main contrib non-free non-free-firmware
```

更新：

```bash
sudo apt update
```

Nvidia 驱动需要编译内核模块 (DKMS)，因此必须安装与当前内核匹配的头文件和编译工具：

```bash
sudo apt install linux-headers-amd64 build-essential dkms -y
```

## 安装 nvidia 驱动

P102 属于 Pascal 架构（GP102核心），Debian 13 默认仓库中的最新版 Nvidia 驱动完全支持该显卡。

安装 NVIDIA 驱动及相关固件:

```bash
sudo apt install nvidia-driver firmware-misc-nonfree -y
```

安装完成后重启:

```bash
sudo reboot
```

## 查看显卡

重启后查看显卡：

```bash
nvidia-smi
```

输出为：

```bash
Sun May 31 20:15:02 2026       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 550.163.01             Driver Version: 550.163.01     CUDA Version: 12.4     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA P102-100                Off |   00000000:03:00.0 Off |                  N/A |
| 53%   24C    P8              8W /  250W |       0MiB /  10240MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
|   1  NVIDIA P102-100                Off |   00000000:04:00.0 Off |                  N/A |
| 51%   27C    P8              8W /  250W |       0MiB /  10240MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|  No running processes found                                                             |
+-----------------------------------------------------------------------------------------+
```

解读一下显卡当前状态：

- 两块显卡全部识别：GPU 0 和 GPU 1 均准确识别为 NVIDIA P102-100。
- 显存完全正常：每块显卡都显示了 10240MiB（标准的 10GB 显存）。
- 驱动和 CUDA 状态：安装的是最新的 550.163.01 稳定版驱动，最高可支持到 CUDA 12.4 环境。
- 温度与功耗极佳：目前待机温度只有 24°C 和 27°C，待机功耗仅 8W，说明散热和系统状态良好。

## 其他配置

开启持久模式 (Persistence Mode):

```bash
sudo nvidia-smi -pm 1
```

为了让系统每次重启都自动保持开启，启用 Nvidia 的持久化守护进程：

```bash
sudo systemctl enable --now nvidia-persistenced
```