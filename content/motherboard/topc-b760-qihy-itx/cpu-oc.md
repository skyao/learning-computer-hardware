---
title: "cpu超频"
linkTitle: "cpu超频"
weight: 20
date: 2023-08-24
description: >
  q1hy CPU超频设置
---

## CPU超频设置

### OverClocking Performance Menu

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

### Processor

高级 -> "OverClocking Performance Menu" -> "Processor"

- Per code disable configuration: 关闭
- core max oc ratio: 0
- per core ratio override: 开启
  - core 0 max ratio： 57
  - core 1 max ratio： 57
  - core 2 max ratio： 57
  - core 3 max ratio： 57
  - core 4 max ratio： 57
  - core 5 max ratio： 57
  - cluster 0 max ratio: 42
  - cluster 1 max ratio: 42
- 核心电压模式：偏移模式
- 覆写大核电压：0
- vf offset mode：lagacy
- vf configuration scope： all-core
- p-core voltage offset： 30     [备注：降压75/50会报错，修改为降压30或者40就能稳定]
  - offset prefix：[-]

- avx2 ratio offset: 1
- Avx2 voltage guardband scale factor: 1
- cpu极限降频温度补偿： 0
- Thermal velocity boost (TVB)
  - Thermal velocity boost : 关闭
  - TVB voltage optimizations: 关闭
  - Enhanced Thermal Velocity Boost: 关闭

### Ring

高级 -> "OverClocking Performance Menu" -> "ring"

- Ring max超频: 0
- Ring Voltage Mode: 偏移模式
- Ring extra turbo voltage： 0
- vf offset mode：lagacy
-  voltage offset： 30  [备注：和 p 核的降压保持一致，修改为降压30或者40]
  - offset prefix：[-]

- Ring Down Bin： 开启
- Min Ring Ratio Llimit： 0 
- Max Ring Ratio Llimit： 0

### GT

高级 -> "OverClocking Performance Menu" -> "GT"

- GT Overclocking frequency： 33
- GT Voltage Mode: 偏移模式
- GT extra turbo voltage： 0
-  GT voltage offset： 20
  - offset prefix：[-]

### Uncore Voltage Mode

高级 -> "OverClocking Performance Menu" -> "Uncore"

- Uncore Voltage Mode: 覆写模式
-  Uncore voltage override： 980     [备注：目标1.35v，-0.29=1.026=1026，这时 SA 电压]
- vccsa voltage offset： 0
  - offset prefix：[+]

### Turbo Ratio Limit Options

高级 -> "Turbo Ratio Limit Options"

- P-core Turbo Ratio Limit Mumcore0: 1
- P-core Turbo Ratio Limit Mumcore1: 2
- P-core Turbo Ratio Limit Mumcore2: 3
- P-core Turbo Ratio Limit Mumcore3: 4
- P-core Turbo Ratio Limit Mumcore4: 5
- P-core Turbo Ratio Limit Mumcore5: 6
- P-core Turbo Ratio Limit Mumcore6: 7
- P-core Turbo Ratio Limit Mumcore7: 8
- P-core Turbo Ratio Limit Ratio0: 57
- P-core Turbo Ratio Limit Ratio1: 57
- P-core Turbo Ratio Limit Ratio2: 56
- P-core Turbo Ratio Limit Ratio3: 56
- P-core Turbo Ratio Limit Ratio4: 55
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


## 电源设置

### power management configuration

"power" -> “power management configuration”

- EUP：开启

### Power & performance

"Advanced" -> "Advanced" -> "Power & performance"



"Cpu - power management control"

- "Platform PL1 power": 200000  (200瓦)
- "Platform PL2 power": 253000  (253瓦)

## 温度表现

### 第一套配置

用的是利民 axp120 x67 下压式散热器，高度67毫米，12015或者12025风扇（可选）。

cpuz 压力测试，维持100瓦的稳定发热。

| 温度 |   散热器   |      风扇       | 均热板硅脂 |  核心硅脂  |
| :--: | :--------: | :-------------: | :--------: | :--------: |
| 76度 | axp120 x67 | 12015（1500转） |  7921硅脂  | 7950相变片 |
| 76度 | axp120 x67 | 12025（1350转） |  7921硅脂  | 7950相变片 |
|      |            |                 |            |            |

观察到：

- 核心4温度最高，核心6温度最低，核心2其次
- 12015换12025风扇温度不变
- 散热器不热，风扇吹出来的风也不热，但温度还是有76度之高，感觉是积热了。

尝试重新涂抹均热板和风扇之间的硅脂



### 第二套配置

用的是乔思伯 hp-500 下压式散热器，高度40毫米，9010厘米风扇。