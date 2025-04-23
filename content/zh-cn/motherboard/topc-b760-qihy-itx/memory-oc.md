---
title: "内存超频"
linkTitle: "内存超频"
weight: 30
date: 2023-08-24
description: >
  q1hy内存超频设置
---

## 内存超频设置

### 内存参数

"Turbo"

- Memory profile： 选 custom profile
- Maximum Memory Frequency： 3600 或者 4000
- Memory Voltage VDDQ： 1.350v，这是内存电压，而且最高只能1.35v。
- Memory Reference Clock：选 100
- Gear Mode：3600可以选 Gear1，4000选 auto
- memory custom profile control：这是内存时序

### 宏基 ddr4 36000 16gx2 （海力士DJR）

|          | 3600  | 4000  |
| -------- | ----- | ----- |
| tCL      | 18    | 19    |
| tRCD/tRP | 22    | 23    |
| tRAS     | 42    | 40    |
| tCWL     | 20    | 20    |
| tFAW     | 44    | 16    |
| tREFI    | 14055 | 65535 |
| tRFC     | 630   | 520   |
| TRRD     | 0     | 4     |
| TRTP     | 12    | 6     |
| TWR      | 24    | 12    |
| TWTR     | 0     | 0     |
| tRFCpb   | 0     | 0     |
| TRFC2    | 468   | 400   |
| TRFC4    | 288   | 250   |
| TRRD_L   | 9     | 8     |
| TRRD_S   | 6     | 4     |
| TWTR_L   | 14    | 10    |
| TCCD_L   | 8     | 8     |
| TWTR_S   | 5     | 4     |
| NMODE    | 1     | 1     |

### 添百事 ddr4 3600 16gx2（海力士 cjr）

Sa 电压 1.30v = 1300 - 280 = 1020

|          | 4000（抄作业） | 4000                    |
| -------- | -------------- | ----------------------- |
| 延迟     | 52.5ns         | 53.6ns（53.6ns）        |
| 结果     | 过不了tm5      | 可以过tm5，注意内存散热 |
| tCL      | 17             | 18（17一分钟蓝屏）      |
| tRCD/tRP | 21             | 21                      |
| tRAS     | 38             | 40（38 会报错）         |
| tCWL     | 20             | 20                      |
| tFAW     | 16             | 16                      |
| tREFI    | 65535          | 65535                   |
| tRFC     | 520            | 660（）                 |
| TRRD     | 4              | 4                       |
| TRTP     | 6              | 6                       |
| TWR      | 12             | 12                      |
| TWTR     | 0              | 0                       |
| tRFCpb   | 0              | 0                       |
| TRFC2    | 387            | 660                     |
| TRFC4    | 238            | 660                     |
| TRRD_L   | 8              | 8                       |
| TRRD_S   | 4              | 4                       |
| TWTR_L   | 10             | 10                      |
| TCCD_L   | 8              | 8                       |
| TWTR_S   | 4              | 4                       |
| NMODE    | 1              | 1                       |



