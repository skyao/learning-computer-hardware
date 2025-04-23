---
title: "HP544+ 网卡测速"
linkTitle: "测速"
weight: 40
date: 2022-03-10
description: >
  对 HP544+ 网卡进行速度测试
---



使用的软件主要是 iperf 和 iperf3，主要原因是某些情况下 iperf3 无法跑满带宽，因此需要补充 iperf 的测试。

## 软交换机器的测速

有一台技嘉 x99 ud4 机器，插了四块 HP544+ 网卡，都跑在 pcie 3.0 8x 上，cpu 是 e5 2680 v4 14核28线，物理机方式安装 debian 12 操作系统。使用系统默认的驱动。机器 IP 地址为 192.168.0.99 。

安装 iperf 和 iperf3 并设置 iperf3 自动启动。

```bash
$ uname -a 
Linux switch99 6.1.0-18-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.76-1 (2024-02-01) x86_64 GNU/Linux

$ iperf --version                               
iperf version 2.1.8 (12 August 2022) pthreads

$ iperf3 --version                      
iperf 3.12 (cJSON 1.7.15)
Linux switch99 6.1.0-18-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.76-1 (2024-02-01) x86_64
Optional features available: CPU affinity setting, IPv6 flow label, SCTP, TCP congestion algorithm setting, sendfile / zerocopy, socket pacing, authentication, bind to device, support IPv4 don't fragment
```

### 本机测速

首先尝试 iperf3 访问本机的速度，即 iperf3 服务器端和 iperf3 客户端都在本地运行

```bash
$ iperf3 -c 192.168.0.99 -t 30 -i 1 -P 5
Connecting to host 192.168.0.99, port 5201
[  5] local 192.168.0.99 port 43692 connected to 192.168.0.99 port 5201
[  7] local 192.168.0.99 port 43702 connected to 192.168.0.99 port 5201
[  9] local 192.168.0.99 port 43710 connected to 192.168.0.99 port 5201
[ 11] local 192.168.0.99 port 43712 connected to 192.168.0.99 port 5201
[ 13] local 192.168.0.99 port 43716 connected to 192.168.0.99 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   751 MBytes  6.29 Gbits/sec    0    639 KBytes       
[  7]   0.00-1.00   sec   751 MBytes  6.29 Gbits/sec    0    639 KBytes       
[  9]   0.00-1.00   sec   751 MBytes  6.29 Gbits/sec    0    639 KBytes       
[ 11]   0.00-1.00   sec   751 MBytes  6.29 Gbits/sec    0    639 KBytes       
[ 13]   0.00-1.00   sec   751 MBytes  6.29 Gbits/sec    0    639 KBytes       
[SUM]   0.00-1.00   sec  3.67 GBytes  31.5 Gbits/sec    0 
......
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec    0             sender
[  5]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec                  receiver
[  7]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec    0             sender
[  7]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec                  receiver
[  9]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec    0             sender
[  9]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec                  receiver
[ 11]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec    0             sender
[ 11]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec                  receiver
[ 13]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec    0             sender
[ 13]   0.00-30.00  sec  22.8 GBytes  6.54 Gbits/sec                  receiver
[SUM]   0.00-30.00  sec   114 GBytes  32.7 Gbits/sec    0             sender
[SUM]   0.00-30.00  sec   114 GBytes  32.7 Gbits/sec                  receiver
```

测试出来的带宽只有 32.7 Gbits/sec ，非常的低。

改用 iperf ，启动服务器端

```bash
iperf -s -p 10001 
```

开始客户端测试：

```bash
iperf -c 192.168.0.99 -t 30 -i 1 -P 10 -p 10001
```

测试出来的带宽是 286 Gbits/sec，远远大于 iperf3 测试出来的 32.7:

```bash
iperf -c 192.168.0.99 -t 10 -i 1 -P 10 -p 10001
------------------------------------------------------------
Client connecting to 192.168.0.99, TCP port 10001
TCP window size: 2.50 MByte (default)
------------------------------------------------------------
[  2] local 192.168.0.99 port 54650 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/20)
[  4] local 192.168.0.99 port 54656 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/24)
[  1] local 192.168.0.99 port 54672 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/26)
[ 10] local 192.168.0.99 port 54716 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/17)
[  3] local 192.168.0.99 port 54666 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/23)
[  5] local 192.168.0.99 port 54680 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/24)
[  6] local 192.168.0.99 port 54690 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/25)
[  8] local 192.168.0.99 port 54724 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/24)
[  7] local 192.168.0.99 port 54700 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/24)
[  9] local 192.168.0.99 port 54730 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=319/32741/24)
[ ID] Interval       Transfer     Bandwidth
[  5] 0.0000-1.0000 sec  3.36 GBytes  28.8 Gbits/sec
[  7] 0.0000-1.0000 sec  3.33 GBytes  28.6 Gbits/sec
[  4] 0.0000-1.0000 sec  3.26 GBytes  28.0 Gbits/sec
[  2] 0.0000-1.0000 sec  3.33 GBytes  28.6 Gbits/sec
[  3] 0.0000-1.0000 sec  3.23 GBytes  27.7 Gbits/sec
[  9] 0.0000-1.0000 sec  3.25 GBytes  27.9 Gbits/sec
[  1] 0.0000-1.0000 sec  3.24 GBytes  27.8 Gbits/sec
[  8] 0.0000-1.0000 sec  3.35 GBytes  28.8 Gbits/sec
[  6] 0.0000-1.0000 sec  3.28 GBytes  28.2 Gbits/sec
[ 10] 0.0000-1.0000 sec  3.22 GBytes  27.7 Gbits/sec
[SUM] 0.0000-1.0000 sec  32.8 GBytes   282 Gbits/sec
......
[  5] 0.0000-10.0046 sec  33.9 GBytes  29.1 Gbits/sec
[  4] 0.0000-10.0044 sec  33.0 GBytes  28.4 Gbits/sec
[  2] 0.0000-10.0045 sec  33.0 GBytes  28.3 Gbits/sec
[  9] 0.0000-10.0043 sec  33.3 GBytes  28.6 Gbits/sec
[  8] 0.0000-10.0041 sec  33.1 GBytes  28.5 Gbits/sec
[ 10] 0.0000-10.0050 sec  34.1 GBytes  29.3 Gbits/sec
[  3] 0.0000-10.0044 sec  33.0 GBytes  28.3 Gbits/sec
[  6] 0.0000-10.0041 sec  33.1 GBytes  28.4 Gbits/sec
[  1] 0.0000-10.0042 sec  33.3 GBytes  28.6 Gbits/sec
[  7] 0.0000-10.0045 sec  33.5 GBytes  28.7 Gbits/sec
[SUM] 0.0000-10.0007 sec   333 GBytes   286 Gbits/sec
```

> 备注
>
> 注意并发线程即 -P 参数的影响很大，需要反复测试才能找到测试结果最好的参数。比如同样这台机器测试，-P 不同的结果是这样的：
>
> - `-P 1`: 33.1 Gbits/sec
> - `-P 2`: 61.3 Gbits/sec
> - `-P 2`: 147 Gbits/sec
> - `-P 8`: 242 Gbits/sec
> - `-P 10`: 288 Gbits/sec
> - `-P 12`: 225 Gbits/sec
> - `-P 20`: 163 Gbits/sec
>
> 能看到 10 个测试线程可以跑到最高带宽 288 G，之后加大线程反而跑的更慢。

### 从其他机器访问

从其他机器访问，测试的这几台机器都是安装的 pve 8.1，linux 6.5 内核，同样是采用的操作系统自带的驱动。

#### 物理网卡访问

在 pve 下直接访问软交换机器，此时可以视为两台物理机用网卡直连。

```bash
iperf -c 192.168.0.99 -P 10 -t 10 -i 1 -p 10001
```

测试结果为 47.6 Gbits/sec，根据经验，这是物理机模式下两块 hp544+ 直连能跑出来的最大带宽了。

```bash
iperf -c 192.168.0.99 -P 10 -t 10 -i 1 -p 10001 
------------------------------------------------------------
Client connecting to 192.168.0.99, TCP port 10001
TCP window size: 16.0 KByte (default)
------------------------------------------------------------
[  4] local 192.168.0.19 port 54790 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/176)
[  2] local 192.168.0.19 port 54788 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/149)
[  1] local 192.168.0.19 port 54798 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/124)
[  7] local 192.168.0.19 port 54806 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/113)
[  5] local 192.168.0.19 port 54808 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/81)
[  3] local 192.168.0.19 port 54800 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/106)
[ 10] local 192.168.0.19 port 54840 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/91)
[  9] local 192.168.0.19 port 54830 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/49)
[  6] local 192.168.0.19 port 54810 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/86)
[  8] local 192.168.0.19 port 54824 connected with 192.168.0.99 port 10001 (icwnd/mss/irtt=14/1448/71)
[ ID] Interval       Transfer     Bandwidth
[ 10] 0.0000-1.0000 sec   805 MBytes  6.75 Gbits/sec
[  4] 0.0000-1.0000 sec   189 MBytes  1.59 Gbits/sec
[  9] 0.0000-1.0000 sec   804 MBytes  6.75 Gbits/sec
[  1] 0.0000-1.0000 sec   194 MBytes  1.63 Gbits/sec
[  8] 0.0000-1.0000 sec   798 MBytes  6.70 Gbits/sec
[  6] 0.0000-1.0000 sec   362 MBytes  3.04 Gbits/sec
[  7] 0.0000-1.0000 sec   448 MBytes  3.75 Gbits/sec
[  5] 0.0000-1.0000 sec   808 MBytes  6.78 Gbits/sec
[  3] 0.0000-1.0000 sec   445 MBytes  3.74 Gbits/sec
[  2] 0.0000-1.0000 sec   815 MBytes  6.84 Gbits/sec
[SUM] 0.0000-1.0000 sec  5.54 GBytes  47.6 Gbits/sec
......
[ 10] 0.0000-10.0117 sec  7.91 GBytes  6.79 Gbits/sec
[  9] 0.0000-10.0115 sec  7.92 GBytes  6.79 Gbits/sec
[  1] 0.0000-10.0116 sec  2.00 GBytes  1.72 Gbits/sec
[  6] 0.0000-10.0115 sec  3.84 GBytes  3.29 Gbits/sec
[  5] 0.0000-10.0115 sec  7.81 GBytes  6.70 Gbits/sec
[  2] 0.0000-10.0117 sec  7.93 GBytes  6.80 Gbits/sec
[  8] 0.0000-10.0117 sec  7.87 GBytes  6.75 Gbits/sec
[  3] 0.0000-10.0113 sec  3.97 GBytes  3.41 Gbits/sec
[  7] 0.0000-10.0116 sec  4.01 GBytes  3.44 Gbits/sec
[  4] 0.0000-10.0116 sec  2.08 GBytes  1.79 Gbits/sec
[SUM] 0.0000-10.0048 sec  55.3 GBytes  47.5 Gbits/sec
```

多台类似配置的机器，分别用同样的测试方法，得到的最高测试速度分别是：

- skyserver：47.5 Gbits/sec
- Skyserver2：43.6 Gbits/sec
- skyserver3：47.7 Gbits/sec
- Skyserver4：42.2 Gbits/sec
- Skyserver5：42.6 Gbits/sec
- Skyserver6：42.4 Gbits/sec

同样用 iperf3 测试:

```bash
iperf3 -c 192.168.0.99 -P 10 -t 10 -i 1 
```

测试结果是 39.3 Gbits/sec :

```bash
iperf3 -c 192.168.0.99 -P 10 -t 10 -i 1        
Connecting to host 192.168.0.99, port 5201
[  5] local 192.168.0.19 port 40664 connected to 192.168.0.99 port 5201
[  7] local 192.168.0.19 port 40672 connected to 192.168.0.99 port 5201
[  9] local 192.168.0.19 port 40674 connected to 192.168.0.99 port 5201
[ 11] local 192.168.0.19 port 40678 connected to 192.168.0.99 port 5201
[ 13] local 192.168.0.19 port 40688 connected to 192.168.0.99 port 5201
[ 15] local 192.168.0.19 port 40700 connected to 192.168.0.99 port 5201
[ 17] local 192.168.0.19 port 40706 connected to 192.168.0.99 port 5201
[ 19] local 192.168.0.19 port 40716 connected to 192.168.0.99 port 5201
[ 21] local 192.168.0.19 port 40730 connected to 192.168.0.99 port 5201
[ 23] local 192.168.0.19 port 40734 connected to 192.168.0.99 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec   432 MBytes  3.62 Gbits/sec    0    266 KBytes       
[  7]   0.00-1.00   sec   432 MBytes  3.62 Gbits/sec    0    267 KBytes       
[  9]   0.00-1.00   sec   432 MBytes  3.62 Gbits/sec    0    205 KBytes       
[ 11]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    214 KBytes       
[ 13]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    314 KBytes       
[ 15]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    293 KBytes       
[ 17]   0.00-1.00   sec   432 MBytes  3.62 Gbits/sec    0    233 KBytes       
[ 19]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    187 KBytes       
[ 21]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    310 KBytes       
[ 23]   0.00-1.00   sec   431 MBytes  3.61 Gbits/sec    0    315 KBytes       
[SUM]   0.00-1.00   sec  4.22 GBytes  36.1 Gbits/sec    0   
......
[SUM]   0.00-10.00  sec  45.8 GBytes  39.3 Gbits/sec    0             sender
[SUM]   0.00-10.00  sec  45.8 GBytes  39.3 Gbits/sec                  receiver
```

还是比 iperf 速度慢不少。



#### sriov 虚拟网卡测试



#### vmbr 网卡测试



### 交换速度

其他两台机器通过软交换机相互访问的速度。

- skyserver：充当 iperf 服务器端
- Skyserver2：42.4 Gbits/sec
- skyserver3：36.2 Gbits/sec （和 skyserver 共用同一块网卡，受限于pcie3.0 8x 64g的总带宽）
- Skyserver4：42.3 Gbits/sec
- Skyserver5：42.2 Gbits/sec
- Skyserver6：42.2 Gbits/sec
