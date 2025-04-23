---
title: "机器2"
linkTitle: "机器2"
weight: 60
date: 2023-08-24
description: >
  q1hy 机器2超频设置
---

## 机器硬件配置

这台 q1hy 机器的硬件配置是：

- cpu/主板： q1hy，天邦官方买的，体制较差
- 内存：PDD买的添百式ddr4 3600 16Gx2 
- 机箱：七彩虹ch160 itx机箱
- 散热：普通4热管单体散热器
- 电源：惠普300瓦

## 主板 bios 设置

参考 b 站视频： https://www.bilibili.com/video/BV1QN4y127iB/

下载地址： https://pan.baidu.com/s/1mdRQS7f5pYQWUF7B3SjIZQ?pwd=q1hy 

选择了 Q1HY-ITX-D4-BIOS2024.05.13.zip 这个 bios，刷新之后断电启动，然后 F9 载入默认配置，重启后再重新配置。

以下 bios 配置基于这个版本的 bios。

### CPU超频设置

#### OverClocking Performance Menu

高级 -> "OverClocking Performance Menu"

- OverCloking Feature: 开启
- Undervolt protection: 关闭
- Per-core HT Disable： 0
- wdt enable： 关闭
- bclk frequency： 100 MHz
- bclok 频率： 0
- bclk aware adaptive voltage： 开启
- fll overclock mode enable： 关闭
- sa pll frenquncy override： 3200 Mhz
- bclk tsc hw fixup： 开启

> 备注：以上内容都是默认，没有改动。

#### Processor

高级 -> "OverClocking Performance Menu" -> "Processor"

- Per code disable configuration: 关闭
- core max oc ratio: 0
- per core ratio override: 开启
  - core 0 max ratio： 56
  - core 1 max ratio： 56
  - core 2 max ratio： 56
  - core 3 max ratio： 56
  - core 4 max ratio： 56
  - core 5 max ratio： 56
  - cluster 0 max ratio: 42
  - cluster 1 max ratio: 42
- 核心电压模式：偏移模式
- 覆写大核电压：0
- vf offset mode：lagacy
- vf configuration scope： all-core
- p-core voltage offset： 30
  - offset prefix：[-]

- avx2 ratio offset: 6
- Avx2 voltage guardband scale factor: 6
- cpu极限降频温度补偿： 0
- Thermal velocity boost (TVB)
  - Thermal velocity boost : 关闭
  - TVB voltage optimizations: 关闭
  - Enhanced Thermal Velocity Boost: 关闭

> 备注：体质稍差，单核设置为 5.6g 而不是 5.7g。

#### Ring

高级 -> "OverClocking Performance Menu" -> "ring"

- Ring max超频: 47  [备注：默认就是47]
- Ring Voltage Mode: 偏移模式
- Ring extra turbo voltage： 0
- vf offset mode：lagacy
-  voltage offset： 0 
  - offset prefix：[+]

- Ring Down Bin： disabled
- Min Ring Ratio Llimit： 8
- Max Ring Ratio Llimit： 45

#### GT

高级 -> "OverClocking Performance Menu" -> "GT"

- GT Overclocking frequency： 33
- GT Voltage Mode: 偏移模式
- GT extra turbo voltage： 0
-  GT voltage offset： 20
  - offset prefix：[-]

#### Uncore

高级 -> "OverClocking Performance Menu" -> "Uncore"

- Uncore Voltage Mode: 覆写模式
-  Uncore voltage override： 1000 
- vccsa voltage offset： 0
  - offset prefix：[+]

#### Turbo Ratio Limit Options

高级 -> "Turbo Ratio Limit Options"

- P-core Turbo Ratio Limit Mumcore0: 1
- P-core Turbo Ratio Limit Mumcore1: 2
- P-core Turbo Ratio Limit Mumcore2: 3
- P-core Turbo Ratio Limit Mumcore3: 4
- P-core Turbo Ratio Limit Mumcore4: 5
- P-core Turbo Ratio Limit Mumcore5: 6
- P-core Turbo Ratio Limit Mumcore6: 7
- P-core Turbo Ratio Limit Mumcore7: 8
- P-core Turbo Ratio Limit Ratio0: 56
- P-core Turbo Ratio Limit Ratio1: 56
- P-core Turbo Ratio Limit Ratio2: 55
- P-core Turbo Ratio Limit Ratio3: 55
- P-core Turbo Ratio Limit Ratio4: 54
- P-core Turbo Ratio Limit Ratio5: 54
- P-core Turbo Ratio Limit Ratio6: 54
- P-core Turbo Ratio Limit Ratio7: 54
- E-core Turbo Ratio Limit Mumcore0: 1
- E-core Turbo Ratio Limit Mumcore1: 2
- E-core Turbo Ratio Limit Mumcore2: 3
- E-core Turbo Ratio Limit Mumcore3: 4
- E-core Turbo Ratio Limit Mumcore4: 5
- E-core Turbo Ratio Limit Mumcore5: 6
- E-core Turbo Ratio Limit Mumcore6: 7
- E-core Turbo Ratio Limit Mumcore7: 8
- E-core Turbo Ratio Limit Ratio0: 42
- E-core Turbo Ratio Limit Ratio1: 42
- E-core Turbo Ratio Limit Ratio2: 42
- E-core Turbo Ratio Limit Ratio3: 42
- E-core Turbo Ratio Limit Ratio4: 42
- E-core Turbo Ratio Limit Ratio5: 42
- E-core Turbo Ratio Limit Ratio6: 42
- E-core Turbo Ratio Limit Ratio7: 42

### 显卡设置

"Advanced" -> "Graphics configuration"：

- Primary Display: PEG Slot  # 外接显卡优先
- VT-d: enabled
- Internal Graphics: enabled
- Above 4gb mmio bios assignment: enabled

"Advanced" -> "pci subsystem settings"：

- pcie  speed: gen4
- above 4g decoding: enabled
- re-size bar support: enabled

### 电源设置

#### power management configuration

"power" -> “power management configuration”

- EUP：开启

#### Power & performance

"Advanced" -> "Advanced" -> "Power & performance"

"Cpu - power management control"

- "Platform PL1 power": 180000  (180瓦)
- "Platform PL2 power": 180000  (160瓦)

备注：在这块主板上不知道为什么上述功耗墙设置会无效，重启之后会自动变成200瓦特。反复测试过几次都是这样，最好发现可以在另外一个地方设置功耗墙，然后这里的功耗墙配置就能修改过来。

改动的地方在 "Turbo" 菜单下：

- CPU Power Limit Override: Manual
- Platform PL1 Enable: enabled
- Platform PL1 Power: 180000
- Platform PL1 Time Windows: 1
- Platform PL2 Enable: enabled
- Platform PL2 Power: 180000

"Power" -> "Power Management Configuration"

- EUP: disabled
- restore ac power loss: power on

### 风扇设置

#### cpu_fan

- cpu_fan mode: manual mode
- temperature 1: 45
- temperature 2: 55
- temperature 3: 65
- temperature 4: 75
- fd/rpm 1: 50
- fd/rpm 2: 100
- fd/rpm 3: 150
- fd/rpm 4: 230
- critical temperature: 80

### 内存超频设置

内存是 ddr4 36000 16gx2 （海力士CJR）

"Turbo"

- turbo mode: enabled
- ac loadline: 65
- dc loaline: 170

- memory profile: custom profile
- maximum memory frequency: 4000
- memory voltage vddq: 1.35v
- memory reference closk: 100
- gear mode: gear1

"Turbo" -> "memory custom profiles control"

| 参数     | 4000  |
| -------- | ----- |
| tCL      | 18    |
| tRCD/tRP | 21    |
| tRAS     | 40    |
| tCWL     | 20    |
| tFAW     | 16    |
| tREFI    | 65535 |
| tRFC     | 540   |
| TRRD     | 4     |
| TRTP     | 6     |
| TWR      | 12    |
| TWTR     | 0     |
| tRFCpb   | 0     |
| TRFC2    | 400   |
| TRFC4    | 250   |
| TRRD_L   | 8     |
| TRRD_S   | 4     |
| TWTR_L   | 10    |
| TCCD_L   | 8     |
| TWTR_S   | 4     |
| NMODE    | 1     |



