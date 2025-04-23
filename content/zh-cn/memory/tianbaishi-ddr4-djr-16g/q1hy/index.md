---
title: "q1hy超频设置"
linkTitle: "q1hy"
weight: 20
date: 2021-03-10
description: 添百世16G cjr内存在天邦 q1hy 平台上的超频设置
---


主板天邦 q1hy，itx主板，16g x 2。

> 备注：由于主板限制，内存电压最高只能设置到 1.35v，因此频率和小参都会比在 z690+13700k 平台上低。

## 超频参数设置

1.35v 跑 ddr4 4000, q1hy 主板 bios 设置内存参数如下：

"Turbo"：

- memory profile: custom profile
- maximum memory frequency: 4000
- memory voltage vddq: 1.35v
- memory reference closk: 100
- gear mode: gear1

"Turbo" -> "memory custom profiles control"：

| 参数     | 4000  |
| -------- | ----- |
| tCL      | 19    |
| tRCD/tRP | 21    |
| tRAS     | 40    |
| tCWL     | 20    |
| tFAW     | 16    |
| tREFI    | 65535 |
| tRFC     | 660   |
| TRRD     | 4     |
| TRTP     | 6     |
| TWR      | 12    |
| TWTR     | 0     |
| tRFCpb   | 0     |
| TRFC2    | 660   |
| TRFC4    | 660   |
| TRRD_L   | 8     |
| TRRD_S   | 4     |
| TWTR_L   | 10    |
| TCCD_L   | 8     |
| TWTR_S   | 4     |
| NMODE    | 1     |

