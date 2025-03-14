---
title: "华硕z690-p d4 + i7 13700k"
linkTitle: "z690 + i7 13700k"
weight: 10
date: 2021-03-10
description: 金百达银爵32G内存在华硕z690-p d4 + i7 13700k平台上的超频设置
---


主板华硕z690-p d4，cpu是13700k。

## 超频4100参数设置

32g x 2，跑ddr4 4100, 参数如下：

```properties
BCLK Frequency : DRAM Frequency Ratio [100:100]
Memory Controller : DRAM Frequency Ratio [1:1]
DRAM Frequency [DDR4-4100MHz]
......
CPU System Agent Voltage: Manual Mode
  - CPU System Agent Voltage Override: 1.400
DRAM Voltage[1.3750]
IVR Transmitter VDDQ Voltage: 1.43
```

DRAM Timing Control:


```properties
DRAM CAS# Latency [18]
DRAM RAS# to CAS# Delay [22]
DRAM RAS# PRE Time [22]
DRAM RAS# ACT Time [42]
DRAM Command Rate [2N]
DRAM RAS# to RAS# Delay L [9]
DRAM RAS# to RAS# Delay S [7]
DRAM REF Cycle Time [631]
DRAM REF Cycle Time 2 [469]
DRAM REF Cycle Time 4 [289]
DRAM Refresh Interval [65535]
DRAM WRITE Recovery Time [12]
DRAM READ to PRE Time [12]
DRAM FOUR ACT WIN Time [38]
DRAM WRITE to READ Delay [6]
DRAM WRITE to READ Delay L [6]
DRAM WRITE to READ Delay S [6]
DRAM CAS to CAS Delay L [8]
DRAM CKE Minimum Pulse Width [6]
DRAM Write Latency [16]
```

aida64 内存测试结果：

![](images/aida64-benchmark-kingbank-32gx2-4100c18.png)

## 超频4200参数设置

套用海盗船内存的参数之后，（tCL放宽到19，tRCD放宽到23），可以跑ddr4200：

```properties
tCL       19
tRCD      23
tRP       23
tRAS      42
CR 2N
tRRD_L 12
tRRD_S 9
tRFC 660
tRFC2 400   
tRFC4 250
tREFI 65535
tWR 24
tRTP   12
tFAW 24 
tWTR 5
tWTR_L 15
tWTR_S 6 
tCCD_L 8
tCKE 12
tWL 20
```

此时内存电压 1.40v，sa电压 1.20，vddq电压 1.38，测试通过。

aida64 内存测试结果：

![](images/aida64-benchmark-kingbank-32gx2-4200c19.png)

> 备注: 这两条内存后来被用在 q1hy 主板上，因此没有再仔细测试过在 z690 + 13700k 平台上的极限性能和小参优化。
>
> 如果有需要，可以参考介绍中的两个作业，理论上可以 4200 c18。