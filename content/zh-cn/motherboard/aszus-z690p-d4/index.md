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

bios 升级到最新版本 3802.

## bios 设置

### 常规设置

F7 进入bios高级设置，“Exit”  --> "Load Optimized Default".

"Advanced" --> "CPU Configuration"  --> "CPU - Power Management Control":

- Boot performance mode: "Max Non-Turbo Performace"
- CPU c-state： enabled
- Package C State Limit： auto

"Advanced" -> "PCI subsystem Settings":

- SR-IOV Support [Enabled]

"Advanced" -> "APM Configuration":

- Restore AC Power Loss: power on

"Monitor" --> "Q-Fan Tuning"
- CPU Fan Q-Fan Control: pwm mode
- CPU Fan Profile: Silent
- 其他类似

"Boot" --> "Boot Configuration"

- Fast Boot: disabled
- Boot Logo Display: disabled
- Bootup Numlock State: off
- Wait For 'F1' If Error: disabled
- Setup Mode: Advanced Mode

### cpu超频设置

不追求极致性能，只追求稳定，因此只简单设置一下 cpu 频率和电压。

"AI Tweaker"

- Performance Preferences: asus advanced oc profile
- Ai Overclock Tuner: "Manual"
- Asus MultiCore Enhancement: disabled - enfore all limits
- Performance Core Ratio: "Sync All Cores"
   - All-Core Ratio Limit: 54
- Efficific Core Ratio: "Sync All Cores"
   - All-Core Ratio Limit: 42
- AVX related Controls:
   - avx2: enabled
   - avx2 ratio offset to per-core ratio limit: user specify
   - avx2 ratio offset: 10
- DIGI+ VRM:
   - CPU Load-line Calibration: Level 6
- Internal CPU Power Management:
   - maximum cpu core temperature: 100
   - Long Duration Package Power Limit: 253
   - Short Duration Package Power Limit: 280
- Ring Down Bin: "Auto"
- Min. CPU Cache Ratio: 8
- Max. CPU Cache Ratio: 48
- Global Core SVID Voltage: adaptive mode
   - offset mode sign: - 
   - cpu core voltage offset: 0.1
- CPU Syatem Agent Voltage: 1.2v
- CPU Input Voltage [1.90000]


### 内存超频设置

"AI Tweaker"

- BCLK Frequency : DRAM Frequency Ratio [100:100]
- Memory Controller : DRAM Frequency Ratio [1:1]
- DRAM Frequency [DDR4-4000MHz]
- DRAM Voltage [1.35]
- IVR Transmitter VDDQ Voltage [1.35]

"AI Tweaker" -> ""DRAM Timing Control":

```properties
DRAM CAS# Latency [18]
DRAM RAS# to CAS# Delay [22]
DRAM RAS# PRE Time [22]
DRAM RAS# ACT Time [42]
DRAM Command Rate [1N]
DRAM RAS# to RAS# Delay L [12]
DRAM RAS# to RAS# Delay S [9]
DRAM REF Cycle Time [660]
DRAM REF Cycle Time 2 [400]
DRAM REF Cycle Time 4 [250]
DRAM Refresh Interval [65535]
DRAM WRITE Recovery Time [24]
DRAM READ to PRE Time [12]
DRAM FOUR ACT WIN Time [24]
DRAM WRITE to READ Delay [5]
DRAM WRITE to READ Delay L [15]
DRAM WRITE to READ Delay S [6]
DRAM CAS to CAS Delay L [8]
DRAM CKE Minimum Pulse Width [12]
DRAM Write Latency [20]
```

