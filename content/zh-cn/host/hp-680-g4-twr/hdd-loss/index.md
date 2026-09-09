---
title: "硬盘不识别"
linkTitle: "硬盘不识别"
weight: 1
date: 2026-09-09
description: 惠普 G4 塔式机接多块东芝 MG08 大容量机械盘时，冷启动不识别、重启后又正常
---

发帖问问：
https://www.chiphell.com/forum.php?mod=viewthread&tid=2899741&page=1&extra=#pid60622531

接多块东芝 MG08 系列 14T / 16T / 18T 机械硬盘时，塔式机会出现**冷启动不认盘、再启动一次又正常**。功率更小的 SFF 反而没问题。下面是现象、原因和按系统区分的处理办法。

## 现象

三台惠普小主机、同一批东芝 MG08 氦气企业盘：

| 机器 | 电源 | 结果 |
|---|---|---|
| 600 G4 TWR | 400W | 冷启动不认盘，再启动一次正常 |
| 680 G4 TWR | 250W | 同上 |
| 600 G4 SFF | 180W | 接三块机械盘也能冷启动识别 |

补充条件：

- 接 **两块或三块** MG08 时塔式机才会出问题。
- 三台系统盘都不在这些机械盘上：两台问题机分别是 **Windows 10（NVMe）** 和 **飞牛 OS（固态）**，SFF 也是固态系统盘。
- BIOS 已更新到最新。POST 等待试过 5 秒、10 秒，**没有效果**。
- Windows 10 长期开机，目前用「启动后再重启一次」可以接受。
- 飞牛 OS 麻烦：开机发现硬盘不在，会做**存储池检查/扫描**，不能很快进系统，也就没法很快再重启。

## 结论：不是电源瓦数不够

MG08 是 9 碟氦气 7200 转企业盘。同代 9 碟盘（MG09 手册）大约是：

- 起转 12V 峰值约 **1.4A**（约 17W）
- 就绪后空闲约 **4W**
- 起转时间典型 **20 秒**，最长约 **30 秒**
- 厂家要求多盘用 staggered spin-up，间隔 **12 秒以上**；或 12V 分时上电，间隔 **25 秒以上**

三块盘同时起转，峰值大约 50W 出头，稳态只有十几瓦。180W / 250W / 400W 的标称容量都盖得住。

这几台 G4 的硬盘供电也不是电源直出：

- 电源多半是 **12V 单路** 专有电源
- 硬盘 12V/5V 走主板 **`SATAPWR`**（常见 2×2 针）再转出去
- 官方设计按 1～2 块盘 + 光驱

瓶颈在 **主板 SATAPWR + 线材 + 冷启动时序**，不在铭牌瓦数。400W 塔式机多出来的功率往往在 PCIe（给显卡），不在硬盘口。

## 为什么塔式机会挂、SFF 反而稳

冷启动失败、软重启就好、两块以上才出现——这三点对不上「瓦数不够」，但对得上这条链：

1. 关机后碟片停转。按电源键时，两到三块 14/16/18T 同时抢 12V。
2. 塔式机 SATA 供电线更长，还经常一根线串 2～3 个口。4 针接头和细线在冲击电流下压降大，末级硬盘上的 12V 可能掉到规格下限（±10%，约 10.8V）附近。
3. 电压一塌，电机起转变慢，或固件上电自检失败。BIOS / 内核那一轮 SATA 扫描已经结束。
4. 「重新启动」时电源其实没掉（软重启），盘还在转或只需很短的 reset，IDENTIFY 立刻能过。

SFF 能带着三块盘冷启动，通常是：

- 线极短，压降小
- 开机时 CPU / 风扇 / 板卡抢 12V 更少，SATAPWR 上的瞬态更干净
- 180W 小电源往往是高效率、稳压很紧的 12V 模块，冷启动爬升不见得比塔式 250/400W 差

所以会出现反直觉结果：**铭牌功率大的塔式机，硬盘口上的「有效 12V」反而更差。** SFF 也是固态系统盘却三块机械盘都正常，更加说明差别在塔式机 SATAPWR 冷启动瞬态，不在系统盘类型。

## 先排除的两件事

### SATA 3.3 Power Disable（第 3 针）

MG08 是 SATA 3.3，P3 可以当 PWDIS。接口上如果常供 3.3V，盘会拒绝起转。  
典型 PWDIS 是 **怎么重启都不转**。这里是冷启动不行、再启动就行，所以不太像主因，仍可用卡普顿胶带贴掉电源口 **P1–P3**（或只贴 P3）冷启动一次排除。

### 听声音判断

| 冷启动时硬盘表现 | 更可能的原因 |
|---|---|
| 完全安静 | 没 12V/5V，或 PWDIS |
| 嗡一下又停、反复咔嗒 | 12V 掉压，起转失败 |
| 在转，但 BIOS/系统里没有 | 起转太慢，扫描已经结束 |

## POST Delay 为什么没效果

G4 这一代惠普商用 BIOS 把 POST Delay 写成：在**启动操作系统之前**暂停若干秒，方便按 F10 / ESC。它加在 POST **末尾**，不是「先睡一会儿再扫 SATA」。

大致顺序：

1. 上电，12V/5V 上来，硬盘开始起转
2. 芯片组 / AHCI 初始化，COMRESET，发 IDENTIFY
3. 超时的端口当成空口，写进设备列表
4. **这时才进入 POST Delay**
5. 再启动系统

Delay 加长只让第 4 步更久，**不会让第 2 步重做**。试过 5 秒、10 秒无效，和这个顺序对得上。设成 20/30 秒多半也救不了冷启动认盘。

若仍想确认 Delay 有没有意义，冷启动后**不要进系统**，在 Logo 空等约 30 秒，再按 ESC 看 **Storage → Device Configuration**：

- 等了 30 秒再进 BIOS，盘出现了：盘其实能起来，只是第一次扫描太早。Delay 对 BIOS 列表帮助仍有限。
- 等了 30 秒 BIOS 里还是没有：第一次扫描失败后盘没有恢复，Delay 再长也没用。

另外确认 **Fast Boot 关闭**，POST 模式尽量用 Full Boot。Fast Boot 会跳过一部分存储初始化，和 Delay 不是一回事。

## 分时上电

惠普 G4 桌面 BIOS **没有**服务器那种「每隔十几秒发一次 COMRESET」。分时上电只能自己做。被延时的只能是数据盘。

### 现成模块（优先）

搜「SATA 硬盘延时启动」「HDD spin-up delay」。常见是 15 针公对母，内部用 MOS 延时 12V。

| 盘 | 供电 |
|---|---|
| 第 1 块 | 原装 SATAPWR 直通 |
| 第 2 块 | 串约 **8～12 秒** 延时头 |
| 第 3 块 | 串约 **16～25 秒** 延时头 |

注意：

- 买明确写 **延时 12V / 电机** 的。
- 电流按每盘起转约 **2A@12V** 留余量。
- 线尽量短，仍接主板 `SATAPWR`。
- BIOS 第一次扫盘大约在上电后 1～3 秒，后两块可能暂时不在 BIOS 列表里。系统盘在 NVMe 上时，进系统后再枚举即可。

### 自制：只延时 12V

惠普硬盘口一般是 **12V + 5V + 地**：

- **5V 一直供**
- **只切断 12V**（主轴电机），延时后再接通

可用 12V 延时导通继电器 / MOSFET，按 **3A 以上** 选型。继电器接在 12V 正极。不要用杜邦线扛电机电流。

### 零成本试验：悬空 SATA 电源第 11 针

SATA 电源 **P11**：

- **接地**：一上电就转（普通电源线多半这样）
- **悬空**：等主机 COMRESET 再转

用胶带只贴硬盘电源口第 11 针。Intel 桌面 AHCI 往往几乎同时对所有口发 COMRESET，**对 G4 不保证有效**，但可逆，值得先试。

### 不建议当主方案

**PUIS（上电待机）**：盘上电不转，等 SET FEATURES / START UNIT。惠普 BIOS 通常不发这套命令，冷启动会变成彻底没盘。Linux libata 发现 PUIS 后还可能一次拉起所有盘。

换带 Staggered Spin-up 的 HBA 干净但贵，小主机不一定值得。

想让 BIOS 冷启动就认全三块盘，需要硬盘先起好转再 POST（延后 Power Good / 复位）。惠普是专有电源，不建议第一次就改。

## 系统盘在 NVMe / 固态上之后

机器能正常开机，缺的只是数据盘在冷启动这一轮没被认到。软重启能认盘，本质是：**12V 已经稳定之后，SATA 又做了一次 COMRESET**。不必再让 BIOS 冷启动就看到这些机械盘。

### Windows 10

长期开机、多一次重启可以接受，可先维持「启动后再重启一次」。

若想省掉手动重启：任务计划程序，启动后延迟 30 秒，对 **标准 SATA AHCI 控制器**（或 Intel RST）禁用再启用，或「扫描检测硬件改动」。不要乱卸 Intel RST RAID 驱动。

### 飞牛 OS

飞牛开机发现硬盘不在，会做存储池检查/扫描，界面也进不去，**不能指望进系统再重启**。ZFS / mdadm 一旦按「盘不在」去 import，可能降级、超时扫描。应让三块盘都出现后再组池，不要在缺盘界面里强行修复/导入。

正确做法是把等待放在**内核加载之前**。这时电源已经稳定，和 Win10 那次成功的软重启是同一类动作。

#### 先改 GRUB 倒计时

飞牛基于 Debian，走 GRUB。冷启动顺序：

1. 按下电源，12V 已加上
2. BIOS 很快结束
3. **GRUB 菜单**（电源一直开着）
4. 内核加载，AHCI 做 COMRESET，再扫盘
5. 飞牛才开始检查存储池

把第 3 步拉到 **25～30 秒**，内核第一次 COMRESET 时盘往往已经在转。

能 SSH 或只插 NVMe 进一次系统时：

```bash
sudo -i
nano /etc/default/grub
```

改成或补上：

```bash
GRUB_TIMEOUT=30
GRUB_TIMEOUT_STYLE=countdown
GRUB_RECORDFAIL_TIMEOUT=30
```

然后：

```bash
update-grub
reboot
```

- `countdown` 会自动倒数，**不插键盘也能过**（飞牛上 `TIMEOUT=0` 或等键盘时，无键盘可能卡在蓝界面）。
- `GRUB_RECORDFAIL_TIMEOUT` 要一起改，否则「上次启动失败」可能覆盖超时。
- 飞牛升级有可能改回 GRUB，升级后看一眼 `/etc/default/grub`。

冷启动听盘：停在 GRUB 那 30 秒里，机械盘应陆续转起来；倒计时结束后应直接进系统。若 30 秒仍偶发缺一块，可试 45 秒。若等多久内核起来后盘还是没有，就不是扫得太早，而是起转失败。

#### 若 GRUB 等待仍不够：组池前复位 SATA

先查 AHCI 地址：

```bash
lspci | grep -i sata
ls /sys/bus/pci/drivers/ahci/
```

常见是 `0000:00:17.0`。再查存储相关服务名：

```bash
systemctl list-units --type=service | grep -Ei 'zfs|storage|trim|vol|mdadm'
```

`/usr/local/sbin/wait-hdd-ahci.sh`：

```bash
#!/bin/bash
sleep 20
echo 0000:00:17.0 > /sys/bus/pci/drivers/ahci/unbind 2>/dev/null
sleep 1
echo 0000:00:17.0 > /sys/bus/pci/drivers/ahci/bind 2>/dev/null
for i in $(seq 1 30); do
  ls /dev/disk/by-id/ata-TOSHIBA* >/dev/null 2>&1 && exit 0
  sleep 1
done
exit 0
```

```bash
chmod +x /usr/local/sbin/wait-hdd-ahci.sh
```

`/etc/systemd/system/wait-hdd.service`：

```ini
[Unit]
Description=Wait and reset AHCI before storage pool import
DefaultDependencies=no
After=local-fs.target
Before=zfs-import-cache.service zfs-import-scan.service zfs-mount.service
Before=local-fs-pre.target

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/wait-hdd-ahci.sh
TimeoutStartSec=90

[Install]
WantedBy=multi-user.target
```

`Before=` 里的服务若本机不存在，改成上面 `grep` 到的飞牛存储服务。

```bash
systemctl daemon-reload
systemctl enable wait-hdd.service
```

建议先只改 GRUB。AHCI 复位改错服务顺序可能影响挂载。

Linux 下若只是晚出现，也可先试扫描：

```bash
echo "- - -" > /sys/class/scsi_host/host0/scan
```

盘在掉压后卡死时，扫描不够，需要上面的 unbind / bind。

## 建议顺序

**飞牛（680 G4 这台优先）：**

1. `GRUB_TIMEOUT=30` + `countdown`，冷启动看能否直接进系统
2. 不行再加组池前 AHCI 复位
3. 仍不稳再给第 2、3 块加 12V 延时头

**Windows 10：**

维持二次重启即可；或开机延迟 30 秒后禁用/启用 AHCI。

**对照实验（可选）：**

1. 塔式机只接一块冷启动 —— 预期正常
2. 接两块，线尽量短、不要串到最远端
3. 空等 30 秒再看 BIOS / 系统是否出现
4. 胶带贴 P3 排除 PWDIS

不建议为这件事换更大的惠普专有电源：400W 塔式机已经复现了问题，再堆瓦数解决不了 SATAPWR 掉压和同时起转。
