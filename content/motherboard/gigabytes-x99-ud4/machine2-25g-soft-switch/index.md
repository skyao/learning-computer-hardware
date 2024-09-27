---
title: "机器2：25g软交换"
linkTitle: "机器2"
weight: 30
date: 2023-08-24
description: >
  实现25G软交换机兼nas
---



### 功耗情况

整机包括：主板，e5 2650 v4，8gx2 普通内存，gt1030低功耗亮机卡，dc电源。开机进入bios后整机99瓦，进入ubuntu server后，整机待机功耗41.3瓦。

后面安装 debian 12 之后的测试，这个主板 + e5 2630l v3，dc电源。在进入系统之后

- 8gx2 普通内存，r5 240低功耗亮机卡，待机40瓦
- 8gx2 普通内存，不插显卡，无头模式进入系统，待机34瓦，也就是r5 240低功耗亮机卡大概是6瓦的待机功耗
- 将内存从8gx2 普通内存换到16g x 4 的 reg ecc 服务器内存，待机54瓦，也就是每条 16g  reg ecc 服务器内存需要5-6瓦的功耗
- 继续用8gx2 普通内存，不插显卡（基础功耗为34瓦），增加一块cx4121a网卡，待机功耗增加到44瓦，也就是一块cx4121a网卡大概要10瓦待机功耗。

### bios设置

为了尽量降低整机功耗，需要的bios设置：

"M.I.T." -> "Advanced Frequency Settings" -> "Advanced CPU Core Settings"

- Hyper-threading technology: disabled
- cpu enhanced halt (c1e): enabled
- c3 state support: enabled
- c6/c7 state support: enabled
- cpu eist function: enabled

"C hipset"

- Audio controller: disabled
- PCH internal Lan: disabled
- Intel VT for Directed I/O(VT-d): enable
- execute disabled bit: enabled
- intel Virutalization Technology(vt-x): enabled

### 机器硬件

cpu 用的是 e5 1660 v3，超全核4.5g，cpuz测试成绩如下：

![](images/cpuz_e51660v3_4.5g.png)

内存用的是海力士 reg ecc 内存，ddr4 2133，afr 颗粒，四条单根4g内存组成4通道16g。

![](images/afr_3200_c16_30000.png)

## 主板bios超频设置

超频 cpu 到全核 4.5g：

M.I.T. -> "Advanced Frequeency Settings"

```properties
CPU Clock Ratio = 45
```

M.I.T. -> "Advanced Voltage Settings" -> "CPU Core Voltage Control"

```properties
CPU VRIN External Override = 1.9v
CPU Vcore = 1.350v
```

超频 内存 到 ddr4 3200：

M.I.T. -> "Advanced Frequeency Settings"

```properties
System Memory Multiplier = 32.00
```

M.I.T. -> "Advanced Memory Settings"

```properties
Memory Timing Mode = Advanced Manual
```

M.I.T. -> "Advanced Memory Settings" -> "Channel A Memory Sub Timmings"

```properties
Cas Latency = 16
tRCD = 20
tRP = 20
tRAS = 40
rRFC = 660
Command Rate(tCMD) = 2 要不要改成1t？
tREFI = 30000
```

Channel B/C/D 同样设置。

M.I.T. -> "Advanced Voltage Settings" -> ""

```properties
DRAM Voltage (Ch A/B) = 1.40v
DRAM Training Voltage (Ch A/B) = 1.40v
DRAM Voltage (Ch C/D) = 1.40v
DRAM Training Voltage (Ch C/D) = 1.40v
```


## 主板bios超频设置2


技嘉x99 ud4 主板 + e5 1660 v3 cpu + 英睿达内存8gx4. 

## 基本设置

### 风扇设置

"M.I.T." -> "smart fan settings"

- "cpu": silent
- "2nd System": 用来给网卡、ssd进行散热，因为风扇转速低，因此需要设置为 "Full Speed".

### 虚拟化支持

"chipset"

- 设置 "Intel VT for Directed I/O(vt-d)"  为 enabled
- 设置 "Intel Virtualization Technology(vt-x)" 为 enabled

顺便关闭主板自带的声卡和网卡：

- "Audio Controller": disabled
- "PCH Internal LAN": disabled

### bios启动设置

"BIOS"

- "Boot Option Priorities": 设置为只保留pve一个启动项目。
- "Bootup Numlock State": off
- "Full Screen LOGO Show": Enabled

- "Storage Boot Option control":  Disabled
- "Other PCI Device ROM Priotiry": Disabled

### 杂项

"Peripherals"

- "Ambient LED": off (关闭主板的led灯效）

### 通电自动启动

"Power"

- 设置 "AC Back" 为 "Always On"

## 超频设置

### cpu超频

"M.I.T." -> "Advanced Frequency Settings"

- "CPU Clock Ratio": 45 (超频到全核4.5g）

"Advanced CPU Core Settings"

- "Turbo Current Limit(Watts)": 200 瓦，功耗墙 
- "Core Current Limit(Amps)": 250 安，电流墙

"M.I.T." -> "Advanced Voltage Settings" -> "CPU Core Voltage Control"

- "CPU VRIN External Override": 1.900v
- CPU Vcore： 1.300v （cpu电压）



### ring超频

"M.I.T." -> "Advanced Frequency Settings" -> "Advanced CPU Core Settings"

- "Uncore Ratro": 34

"M.I.T." -> "Advanced Voltage Settings" -> "CPU Core Voltage Control"

- "CPU Ring Voltage": 1.10v


### 内存超频

"M.I.T." -> "Advanced Frequency Settings"

- "System Memory Multiplier": 32 (超频到 ddr4 3200 频率）


"M.I.T." -> "Advanced Voltage Settings" -> DRAM Voltage Control"

- "DRAM Voltage（CH A/B）"： 1.250v
- "DRAM Training  Voltage（CH A/B）"： 1.250v
- "DRAM Voltage（CH C/D）"： 1.250v
- "DRAM Training  Voltage（CH C/D）"： 1.250v

"M.I.T." -> "Advanced Memory Settings" -> DRAM Voltage Control"

- "System Memory Multiplier": 32 (超频到 ddr4 3200 频率）
- "Memory Timing Mode": "Advanced Manual"

"Channel A Memory sub Timings" （A/B/C/D要同样设置四次）：

- CAS Latency 16
- tRCD  18
- tRP 18
- tRAS 40

- tRFC 660
- Command Rate(tCMD) 1 


- tREFI 30000

TODO: 遇到一点问题，超频内存之后，各种测试通过，但是进 pve 8.2 时就会卡住。稍后看怎么解决