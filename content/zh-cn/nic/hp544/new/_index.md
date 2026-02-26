---
title: "新系统"
linkTitle: "新系统"
weight: 50
date: 2026-02-19
description: >
  HP544+ 网卡在新硬件和新系统下的使用
---

HP544+ 网卡太老，在新硬件和新系统下使用时，可能会有一些问题。本章节主要记录遇到的问题和解决方法。

### 网卡消失

问题描述：

- 硬件： z790m 主板，14代 cpu，14700hx 处理器
- 操作系统： pve9
- 问题描述： 插了两块 HP544+ 网卡，开机正常。几分钟之后，只有第一槽的网卡正常，另一块插在 pcie 4x 槽的网卡消失了。体现为 lspci 可以看到网卡，但是 ip addr 无法看到。重启后正常，但几分钟后问题依旧，第二块网卡继续消失。

问题分析： 

- PCIe ASPM (节能模式) 冲突

   Z790 是消费级芯片组，默认 BIOS 和 OS 设置会积极地尝试让 PCIe 设备进入睡眠（L0s/L1 状态）以省电。企业级网卡通常不支持这种频繁的电源状态切换，会导致链路不稳定或直接掉卡。

   验证方法：查看系统日志 dmesg。

   ```bash
   dmesg | grep -iE "mlx|pci|error"
   ```

   如果看到类似 unrecoverable error、PCIe Bus Error 或者 mlx4_core: Internal error detected，大概率是这个问题。

- PCIe 版本

   日志显示 limited by 8.0 GT/s PCIe x4 link，说明卡协商在 PCIe 3.0。但是，Z790 芯片组的 PCH 通道默认通常是 PCIe 4.0 甚至自动协商。ConnectX-3 是老设备，信号电气特性可能与 Z790 的自动协商机制打架，导致链路重置（Retraining）

解决方法： 

1. 关闭PCIe ASPM (节能模式) 

   在 PVE 的启动参数中强制关闭 PCIe 节能。

   编辑 grub 配置：

   ```bash
   nano /etc/default/grub
   ```

   找到 GRUB_CMDLINE_LINUX_DEFAULT 这一行，在引号内添加 pcie_aspm=off。

   ```bash
   GRUB_CMDLINE_LINUX_DEFAULT="quiet pcie_aspm=off"
   ```

   更新 grub 并重启：

   ```bash
   update-grub
   reboot
   ```

2. 在主板 bios 中将 pcie 版本设置为 pcie 3.0

3. 在主板 bios 中启用 Above 4G Decoding（大于 4G 地址解码）。

4. 在主板 bios 中关闭 Resizable BAR Support（可调整大小的 BAR）。

设置之后，问题临时解决。但后续发现，长时间之后这块网卡依然会消失，主要原因猜测是 pch 通道的 pcie 通道信号不稳定，导致网卡掉线。

参考：

https://aistudio.google.com/prompts/1-qQ-dKCwzsas-y81yh2x5pcL8JR1Aq75

期间用到的排查问题的命令：

```bash
ip addr
lspci | grep controller
dmesg | grep -iE "mlx|pci|error"
```