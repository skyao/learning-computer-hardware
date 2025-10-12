---
title: "性能"
linkTitle: "性能"
weight: 40
date: 2022-03-10
description: >
  cx-5网卡性能简单测试
---

测试使用的网卡都是 cx-5 100G 网卡，一共有三块：

- 一块原厂cx555a 双头100G网卡，pcie4.0，插在A机器上
- 两块华为 sp350 网卡，单头，分别插在B和C机器上

## iperf 测试

### 直连

A机器和B机器之间直连，速度为 99.0 Gbits/sec：

```bash
[  2] 2.0000-3.0000 sec  3.84 GBytes  33.0 Gbits/sec
[  3] 2.0000-3.0000 sec  3.84 GBytes  33.0 Gbits/sec
[  1] 2.0000-3.0000 sec  3.85 GBytes  33.0 Gbits/sec
[SUM] 2.0000-3.0000 sec  11.5 GBytes  99.0 Gbits/sec
[  2] 3.0000-4.0000 sec  3.85 GBytes  33.0 Gbits/sec
[  3] 3.0000-4.0000 sec  3.84 GBytes  33.0 Gbits/sec
[  1] 3.0000-4.0000 sec  3.84 GBytes  32.9 Gbits/sec
[SUM] 3.0000-4.0000 sec  11.5 GBytes  99.0 Gbits/sec
[  2] 4.0000-4.8596 sec  3.28 GBytes  32.8 Gbits/sec
[  2] 0.0000-4.8596 sec  18.5 GBytes  32.8 Gbits/sec
[  3] 4.0000-4.8595 sec  3.28 GBytes  32.7 Gbits/sec
[  3] 0.0000-4.8595 sec  18.5 GBytes  32.7 Gbits/sec
[  1] 4.0000-4.8594 sec  3.25 GBytes  32.5 Gbits/sec
[  1] 0.0000-4.8594 sec  18.9 GBytes  33.4 Gbits/sec
[SUM] 4.0000-4.8506 sec  9.80 GBytes  99.0 Gbits/sec
[SUM] 0.0000-4.8506 sec  55.9 GBytes  99.0 Gbits/sec
```

备注：需要设置 MTU 为 9000,如果是默认的 1500，则测试最大速度为 94.0 Gb。

### vSwitch 软交换

A机器用 open vSwitch 做软交换，B机器和C机器之间通过A机器中转，速度为 71.1 Gbits/sec：

```bash
[  4] 58.0000-59.0000 sec  1.22 GBytes  10.5 Gbits/sec
[  2] 58.0000-59.0000 sec  1.23 GBytes  10.5 Gbits/sec
[  3] 58.0000-59.0000 sec  2.92 GBytes  25.1 Gbits/sec
[  1] 58.0000-59.0000 sec  2.92 GBytes  25.1 Gbits/sec
[SUM] 58.0000-59.0000 sec  8.28 GBytes  71.1 Gbits/sec
[  4] 59.0000-60.0000 sec  1.20 GBytes  10.3 Gbits/sec
[  2] 59.0000-60.0000 sec  1.23 GBytes  10.6 Gbits/sec
[  3] 59.0000-60.0000 sec  2.92 GBytes  25.1 Gbits/sec
[  1] 59.0000-60.0000 sec  2.92 GBytes  25.1 Gbits/sec
[SUM] 59.0000-60.0000 sec  8.28 GBytes  71.1 Gbits/sec
[  4] 0.0000-60.0157 sec  73.8 GBytes  10.6 Gbits/sec
[  2] 0.0000-60.0155 sec  75.3 GBytes  10.8 Gbits/sec
[  1] 0.0000-60.0155 sec   174 GBytes  24.9 Gbits/sec
[  3] 0.0000-60.0155 sec   174 GBytes  24.9 Gbits/sec
[SUM] 0.0000-60.0002 sec   497 GBytes  71.1 Gbits/sec
```

备注：需要设置 MTU 为 9000,如果是默认的 1500，则测试最大速度为 xxx Gb。