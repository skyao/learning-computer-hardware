---
title: "华硕z690-p d4主板"
linkTitle: "华硕z690-p d4"
weight: 9970
date: 2023-08-24
description: >
  中端 z690 ATX大板，主力机器
---



## 主板资料

华硕官方网站：

https://www.asus.com.cn/motherboards-components/motherboards/prime/prime-z690-p-d4/

主板优势：

1. 价格便宜，超频能力尚可，用来超频13700k 和旗舰主板差距不大，够用了。
2. 有四条pcie16x：只有一条是pcie5.0 16x（实际16x）, 两条 pcie3.0 16x（实际4x），一条pcie4.0 16x（实际4x） 

## 

## 主力机 13700kf

13700kf oc 全核 5.6g，小核 4.2g, 搭配 英睿达铂胜8G c9bjz 内存 实现 8g x 4 = 32g 

### bios 2801 定压定频5.6g

主板超频bios设置 (bios 版本 2801）：

Ai tweaker：

```properties
Ai Overclock Tuner [XMP I]
XMP [XMP DDR4-4000 16-21-21-42-1.45V]
BCLK Frequency [Auto]
Intel(R) Adaptive Boost Technology [Enabled]
ASUS MultiCore Enhancement [Enabled – Remove All limits]
SVID Behavior [Auto]
BCLK Frequency : DRAM Frequency Ratio [100:100]
Memory Controller : DRAM Frequency Ratio [1:1]
DRAM Frequency [DDR4-4200MHz]
OC Tuner [Keep Current Settings]
Performance Core Ratio [Sync All Cores]
All-Core Ratio Limit [56]

Efficient Core Ratio [Sync All Cores]
ALL-Core Ratio Limit [42]
```

Ai tweaker -> AVX Related Controls：

```properties
AVX2 [Enabled]
AVX2 Ratio Offset to per-core Ratio Limit [User Specify]
AVX2 Ratio Offset [10]
AVX2 Voltage Guardband Scale Factor [User Specify]
AVX2 Voltage Guardband Scale Factor [10]
```

Ai tweaker -> DRAM Timing Control：

> 备注：用的是 英睿达 c9blm 内存，可以跑 ddr4 4200.

```properties
DRAM CAS# Latency [16]
DRAM RAS# to CAS# Delay [23]
DRAM RAS# PRE Time [23]
DRAM RAS# ACT Time [46]
DRAM Command Rate [2N]
DRAM RAS# to RAS# Delay L [9]
DRAM RAS# to RAS# Delay S [6]
DRAM REF Cycle Time [660]
DRAM REF Cycle Time 2 [660]
DRAM REF Cycle Time 4 [660]
DRAM Refresh Interval [65536]
DRAM WRITE Recovery Time [16]
DRAM READ to PRE Time [12]
DRAM FOUR ACT WIN Time [38]
DRAM WRITE to READ Delay [5]
DRAM WRITE to READ Delay L [12]
DRAM WRITE to READ Delay S [12]
DRAM CAS to CAS Delay L [8]
DRAM CKE Minimum Pulse Width [8]
DRAM Write Latency [18]
```

Ai tweaker -> DIGI+VRM：

```properties
CPU Load-line Calibration [Level 6]
```

Ai tweaker -> DIGI+VRM：

```properties
Maximum CPU Core Temperature [110]
Long Duration Package Power Limit [300]
Short Duration Package Power Limit [300]
IA AC Load Line [0.01]
IA DC Load Line [0.01]
```

Ai tweaker:

```properties
Ring Down Bin [Auto]
Min. CPU Cache Ratio [Auto]
Max. CPU Cache Ratio [Auto]
BCLK Aware Adaptive Voltage [Enabled]
Actual VRM Core Voltage [Manual Mode]
- CPU Core Voltage Override [1.42000]
Global Core SVID Voltage [Auto]
Cache SVID Voltage [Auto]
CPU L2 Voltage [Auto]
CPU System Agent Voltage [Manual Mode]
- CPU System Agent Voltage Override [1.43000]
CPU Input Voltage [1.90000]
DRAM Voltage [1.50000]
IVR Transmitter VDDQ Voltage [1.43000]
PCH 1.8V Primary Voltage [Auto]
```

Advanced:

Advanced -> Platform Misc Configuration:

Advanced -> CPU Configuration:

Advanced -> PCI subsystem Settings:

```properties
Above 4G Decoding [Enabled]
Re-Size BAR Support [Enabled]
SR-IOV Support [Enabled]
```

Advanced -> Restore AC Power Loss:

```properties
Restore AC Power Loss [Power On]
```















#### ~~bios 2404~~

主板超频bios设置 (bios 版本 2404）：

- [z690_5.6g_4200_32g.CMO](images/z690_5.6g_4200_32g.CMO) 
-  [z690_5.6g_4200_32g.txt](images/z690_5.6g_4200_32g.txt) 
-  [z690_5.8g_4200_32g_noht.CMO](../../../../../../../../../media/sky/U16G/z690_5.8g_4200_32g_noht.CMO) 
-  [z690_5.8g_4200_32g_noht.txt](../../../../../../../../../media/sky/U16G/z690_5.8g_4200_32g_noht.txt) 

> 备注：升级到最新的支持14代cpu的bios（2802）之后，这个超频配置不稳定，需要重新调整。





### 开发机 13700k

13700k oc 全核 5.6g，小核 4.2g, 搭配 阿斯加特32G ddr4 2666 (真香条) 实现 32g x 4 = 128g 

![z690_5.6g_3600_128g_setting](images/z690_5.6g_3600_128g_setting.png)

主板超频bios设置 (bios 版本 2404）：

- [z690_5.6g_3600_128g.CMO](images/z690_5.6g_3600_128g.CMO)  
- [z690_5.6g_3600_128g_setting.txt](images/z690_5.6g_3600_128g_setting.txt) 

备注：升级到最新的支持14代cpu的bios（2802）之后，这个超频配置不稳定，需要重新调整。





### 备用机

12400，搭配内存 实现 16g x 2 = 32g 
