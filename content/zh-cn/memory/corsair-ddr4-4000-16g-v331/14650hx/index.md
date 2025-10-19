---
title: "14650hx超频设置"
linkTitle: "14650hx"
weight: 30
date: 2021-03-10
description: 海盗船内存在天邦 14650hx 平台上的超频设置
---


主板天邦 14650hx，matx主板，16g x 2。

## 超频参数设置

1.40v 跑 ddr4 4000, 14650hx 主板 bios 设置内存参数如下：

"Turbo"：

- cpu VDDQ: 1.35v
- memory profile: custom profile
- maximum memory frequency: 4000
- memory voltage vddq: 1.40v
- memory reference closk: 100
- gear mode: gear1

"Turbo" -> "memory custom profiles control"：


| 参数名   | 参数值 |
| -------- | ------ |
| NMODE    | 1      |
| tCL      | 18     |
| tRCD/tRP | 22     |
| tRAS     | 42     |
| tRFC2    | 520    |
|          |        |
| tREFI    | 65535  |
| TWR      | 24     |
| TWR_L    | 15     |
| TWR_S    | 5      |
| TRRD_L   | 11     |
| TRRD_S   | 8      |
| TRTP     | 12     |
| tFAW     | 44     |
| tCWL     | 20     |
| tCKE     | auto   |
| tRFC     | 700    |
| TRRD     | auto   |
| TWTR     | auto   |
| tRFCpb   | auto   |
| TRFC4    | 320    |
| TCCD_L   | 8      |



