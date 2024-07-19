---
title: "13700k盒装"
linkTitle: "13700k盒装"
weight: 10
date: 2023-08-24
description: >
  盒装国行13700k cpu超频
---

这块 13700k 是京东首发买的国行，体质中等，不雕不雷。

## bios超频

主板为华硕 z690-p d4，bios版本为3212（发布于2024-05-24）。

### AI Tweaker

- performance perferences：asus advanced OC profile

- asus multicore enhancement： enabled-remove all limits

- performance core ratio： by core usage
  - 1-Core Ratio Limit: 58
  - 2-Core Ratio Limit: 58
  - 3-Core Ratio Limit: 57
  - 4-Core Ratio Limit: 57
  - 5-Core Ratio Limit: 56
  - 6-Core Ratio Limit: 56
  - 7-Core Ratio Limit: 56
  - 8-Core Ratio Limit: 56

- Efficient Core Ratio: Sync All Cores

  - All-Core Ratio Limit: 45

#### AVX Related Controls

- AVX2: enable
- AVX2 Ratio Offset to per-core Ratio Limit: User Specify
- AVX2 Ratio Offset: 8
- AVX2 Ratio Guardband Scale Factor: User Specify
- AVX2 Ratio Guardband Scale Factor:  8 (?)

#### DIGI+ VRM

- CPU Load-line Calibration: level6

#### Internal CPU Power Management

- Maximun CPU Core Temperature: 110

- Long Duration Package Power Limit: 253

- Short Duration Package Power Limit: 253

#### Tweaker's Paradise 

- Actual VRM Core Voltage: Offset Mode

  - Offset Mode Sign: - 
  - CPU Core Voltage Offset: 0.05

- CPU Input Voltage: 1.9v


## 测试成绩

### cpuz

单核 946， 多核 13263。

压力测试时，功耗238瓦，温度85到95度。

测试期间全核稳定在 5.6g ，电压 1.369v。

### aida64 FPU

FPU压力测试时，功耗238瓦，温度85到92度。

测试期间全核稳定在 5g (最大频率5.8g - avx512 offset 8) ，电压 1.334v。

### aida64 cpu

压力测试时，功耗167瓦，温度70到75度。

测试期间全核稳定在 5.6g ，电压 1.39v。

### r23

r23 10 分钟通过，分数 29844 pts。

功耗大概253瓦（达到功耗墙），温度85到95度（比较奇怪的是温度低于cpu压力测试时的240瓦）。

测试期间全核稳定在 5.2 到 5.3g，电压 1.324v。

