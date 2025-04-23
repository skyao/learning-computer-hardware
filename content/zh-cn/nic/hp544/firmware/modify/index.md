---
title: "修改原厂固件"
linkTitle: "修改固件"
weight: 40
date: 2022-03-10
description: >
  介绍如何修改原厂固件以便支持特性如单模光模块和56G以太网
---

# 说明

## 原文地址

原文请访问：

[544+ flr 解锁56G直连 (github.com)](https://gist.github.com/lzghzr/d37327c218a09cce3e601cea3ebdbd42)

## 修改后的固件下载

修改好的固件请在此下载：

链接:   https://pan.baidu.com/s/1uiebg1P-tTL1WIuxMgLblQ?pwd=tfgi

简单说明：

- powerlevel： 支持单模光模块 （已验证，可用，但我只测试了40g的光模块）
- 56kr4: 支持56g 以太网 （已验证，可用，但我只测试原厂dac线）
- drom：去除 flexboot，这样开机不用在屏幕上显示网卡的字样，可以提供开机速度（应该bios里面也不会增加boot的内容，待验证）

有尝试这几个固件的同学请留言说明验证情况，谢谢！

--------

复制一份原文在此以备不时之需：

# 原文

## 需要的工具

- 固件工具 [NVIDIA Firmware Tools (MFT)](https://network.nvidia.com/products/adapter-software/firmware-tools/)

- 固件工具 4.3.0.25版本 [NVIDIA Firmware Tools (MFT) 4.3.0.25](http://www.mellanox.com/downloads/MFT/WinMFT_x64_4_3_0_25.exe)
- HPE固件 [fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.tgz](https://support.hpe.com/connect/s/softwaredetails?language=en_US&softwareId=MTX_14a9d6b3720f4c6688c824b8b2)
- 自定义固件 [ConnectX3Pro-rel-2_40_5030.tgz](http://content.mellanox.com/firmware/ConnectX3Pro-rel-2_40_5030.tgz)
- 固件ini修改 [mft-scripts](https://github.com/BeTeP-STH/mft-scripts)

## 准备工作

下载所有需要的工具，并且安装 `NVIDIA Firmware Tools (MFT)` 和 `NVIDIA Firmware Tools (MFT) 4.3.0.25`
将 `ConnectX3Pro-rel-2_40_5030.tgz` 里的 `fw-ConnectX3Pro-rel.mlx` 和 `MCX354A-FCC_Ax.ini` 复制到 `NVIDIA Firmware Tools (MFT) 4.3.0.25` 安装目录
我安装在了 `C:\Program Files\Mellanox\WinMFT_x64_4_3_0_25`
再复制一份 `MCX354A-FCC_Ax.ini` 命名为 `MCX354A-FCC_Ax_56G.ini`

再将 `fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.tgz` 里的 `fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin` 和 `mft-scripts` 里的 `fs2_update_ini.py` 复制到 `NVIDIA Firmware Tools (MFT)` 安装目录
我安装在了 `C:\Program Files\Mellanox\WinMFT`

## 生成固件
修改 `MCX354A-FCC_Ax_56G.ini` 内 `[IB]` 部分
```ini
port1_802_3ap_56kr4_ability = true
port2_802_3ap_56kr4_ability = true

port1_802_3ap_cr4_enable = true
port2_802_3ap_cr4_enable = true
port1_802_3ap_cr4_ability = true
port2_802_3ap_cr4_ability = true

port1_802_3ap_kr4_enable = true
port2_802_3ap_kr4_enable = true
port1_802_3ap_kr4_ability = true
port2_802_3ap_kr4_ability = true
```
改为
```ini
port1_802_3ap_56kr4_enable = true
port2_802_3ap_56kr4_enable = true
port1_802_3ap_56kr4_ability = true
port2_802_3ap_56kr4_ability = true

port1_802_3ap_cr4_enable = true
port2_802_3ap_cr4_enable = true
port1_802_3ap_cr4_ability = true
port2_802_3ap_cr4_ability = true

port1_802_3ap_kr4_enable = true
port2_802_3ap_kr4_enable = true
port1_802_3ap_kr4_ability = true
port2_802_3ap_kr4_ability = true
```
分别生成两个固件, 开启56G和不开启的
```PowerShell
C:\Program Files\Mellanox\WinMFT_x64_4_3_0_25> mlxburn -fw fw-ConnectX3Pro-rel.mlx -c MCX354A-FCC_Ax_56G.ini -wrimage MCX354A-FCC_Ax_56G.bin
-W- Removing parameter defined outside a group: "prepMLX version".
-I- Generating image ...
-I- Image generation completed successfully.
```
```PowerShell
C:\Program Files\Mellanox\WinMFT_x64_4_3_0_25> mlxburn -fw fw-ConnectX3Pro-rel.mlx -c MCX354A-FCC_Ax.ini -wrimage MCX354A-FCC_Ax.bin
-W- Removing parameter defined outside a group: "prepMLX version".
-I- Generating image ...
-I- Image generation completed successfully.
```

## 分析固件
通过 `UltraCompare` 对比两个固件, 一共有四处改动

第一处在头部
> |MCX354A-FCC_Ax_56G.bin|MCX354A-FCC_Ax.bin|
> |-|-|
> |00000020h: 00 00 <font color=red>**68 E6**</font> 00 00 00 04 F5 00 00 0B FD 00 3B C8 ;|00000020h: 00 00 <font color=red>**32 AE**</font> 00 00 00 04 F5 00 00 0B FD 00 3B C8 ;|
> |00000030h: 00 0A 99 <font color=red>**48**</font> 00 00 3B 84 00 10 00 40 00 00 01    ;|00000030h: 00 0A 99 <font color=red>**44**</font> 00 00 3B 84 00 10 00 40 00 00 01 85 ;|

第二处在文件靠后位置
> |MCX354A-FCC_Ax_56G.bin|MCX354A-FCC_Ax.bin|
> |-|-|
> 000a7bb0h: 1F <font color=red>**83**</font> F9 00 7F 8F FF 20 00 01 F9 A0 00 8F F0 02 ;|000a7bb0h: 1F <font color=red>**03**</font> F9 00 7F 8F FF 20 00 01 F9 A0 00 8F F0 02 ;
> 000a7bc0h: 03 8F F0 17 00 01 F9 A4 00 40 00 01 00 D3 01 FF ;                       |000a7bc0h: 03 8F F0 17 00 01 F9 A4 00 40 00 01 00 D3 01 FF ;
> 000a7bd0h: 00 01 F9 AC 1F <font color=red>**83**</font> F9 00 7F 8F FF 20 00 01 F9 B0 ;|000a7bd0h: 00 01 F9 AC 1F <font color=red>**03**</font> F9 00 7F 8F FF 20 00 01 F9 B0 ;

第三处在文件末尾前
> |MCX354A-FCC_Ax_56G.bin|MCX354A-FCC_Ax.bin|
> |-|-|
> 000a8fe0h: 00 00 <font color=red>**96 1F**</font> 00 00 00 03 00 00 00 18 00 00 00 00 ;|000a8fe0h: 00 00 <font color=red>**22 2D**</font> 00 00 00 03 00 00 00 18 00 00 00    ;

第四处为文件末尾全部

第四处改动较多, 实际为ini压缩后数据, 第二处改了两个位置, 通过
```ini
port1_802_3ap_56kr4_enable = true
port2_802_3ap_56kr4_enable = true
```
可以猜测, 此处为两个端口的设置项
而第一处和第三处则为校验位

继续分析 `fw-ConnectX3Pro-rel.mlx`, 找到 `port1_802_3ap_56kr4_enable`相关选项
> scratchpad.eth.port[0].mode_40g_is_50g 0x1f99c.5 1
> scratchpad.eth.port[0].b0_hw_eye_opener_cfg_measure_time 0x1f99c.8 4
> scratchpad.eth.port[0].eth_802_3ap_56kr4_ability 0x1f99c.12 1
> scratchpad.eth.port[0].eth_802_3ap_cr4_ability 0x1f99c.13 1
> scratchpad.eth.port[0].eth_802_3ap_kr4_ability 0x1f99c.14 1
> scratchpad.eth.port[0].eth_802_3ap_kr_ability 0x1f99c.15 1
> scratchpad.eth.port[0].eth_802_3ap_kx_ability 0x1f99c.16 1
> scratchpad.eth.port[0].eth_802_3ap_kx4_ability 0x1f99c.17 1
> scratchpad.eth.port[0].eth_802_3ap_kr2_ability 0x1f99c.18 1
> scratchpad.eth.port[0].eth_802_3ap_100M_ability 0x1f99c.19 1
> <font color=red>**scratchpad.eth.port[0].eth_802_3ap_56kr4_enable 0x1f99c.23 1**</font>
> scratchpad.eth.port[0].eth_802_3ap_cr4_enable 0x1f99c.24 1
> scratchpad.eth.port[0].eth_802_3ap_kr4_enable 0x1f99c.25 1
> scratchpad.eth.port[0].eth_802_3ap_kr_enable 0x1f99c.26 1
> scratchpad.eth.port[0].eth_802_3ap_kx_enable 0x1f99c.27 1
> scratchpad.eth.port[0].eth_802_3ap_kx4_enable 0x1f99c.28 1
> scratchpad.eth.port[0].eth_802_3ap_kr2_enable 0x1f99c.29 1
> scratchpad.eth.port[0].eth_802_3ap_100M_enable 0x1f99c.30 1

可以看到此项位置为 `0x1f99c.23`
将第二处改动转为二进制进行对比

`1F83 F900`
|31|30|29|28| |27|26|25|24| |23|22|21|20| |19|18|17|16| |15|14|13|12| |11|10|9|8| |7|6|5|4| |3|2|1|0|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|0|0|0|1| |1|1|1|1| |<font color=red>**1**</font>|0|0|0| |0|0|1|1| |1|1|1|1| |1|0|0|1| |0|0|0|0| |0|0|0|0|

`1F03 F900`
|31|30|29|28| |27|26|25|24| |23|22|21|20| |19|18|17|16| |15|14|13|12| |11|10|9|8| |7|6|5|4| |3|2|1|0|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|0|0|0|1| |1|1|1|1| |<font color=red>**0**</font>|0|0|0| |0|0|1|1| |1|1|1|1| |1|0|0|1| |0|0|0|0| |0|0|0|0|

很明显, 第23位为 `port1_802_3ap_56kr4_enable`, 所以只要修改此位就可以不通过 `mlxburn` 解锁56G

## 制作固件
先到 `NVIDIA Firmware Tools (MFT)` 目录提取一份配置文件
```PowerShell
C:\Program Files\Mellanox\WinMFT> flint -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin dc HP_1380110017.ini
```
转到 `[IB]` 项
```ini
;;speed flags for port0
cx3_spec1_3_ib_support_port0 = 1
cx3_spec1_2_ib_support_port0 = 1
spec1_3_fdr10_ib_support_port0 = 1
spec1_3_fdr14_ib_support_port0 = 1
port1_802_3ap_56kr4_ability = 1
port1_802_3ap_cr4_ability = 1
port1_802_3ap_cr4_enable  = 1
```
可以看到缺少了以下三项
```ini
port1_802_3ap_56kr4_enable = true
port1_802_3ap_kr4_enable = true
port1_802_3ap_kr4_ability = true
```
根据上面分析, 缺失的部分为

> scratchpad.eth.port[0].eth_802_3ap_kr4_ability 0x1f99c.14 1
> scratchpad.eth.port[0].eth_802_3ap_56kr4_enable 0x1f99c.23 1
> scratchpad.eth.port[0].eth_802_3ap_kr4_enable 0x1f99c.25 1

此时设置项应为
|31|30|29|28| |27|26|25|24| |23|22|21|20| |19|18|17|16| |15|14|13|12| |11|10|9|8| |7|6|5|4| |3|2|1|0|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|0|0|0|1| |1|1|<font color=red>**0**</font>|1| |<font color=red>**0**</font>|0|0|0| |0|0|1|1| |1|<font color=red>**0**</font>|1|1| |1|0|0|1| |0|0|0|0| |0|0|0|0|

转换成16进制 `1D03 B900`
使用 `UltraEdit` 查找 `1D03 B900`
> |fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin|
> |-|
> |000ed760h: 00 01 F9 9C <font color=red>**1D 03 B9 00**</font> 7F 8F FF 20 00 01 F9 A0 ;|
> |000ed770h: 00 8F F0 02 03 8F F0 17 00 01 F9 A4 00 40 00 01 ;|
> |000ed780h: 00 D3 01 FF 00 01 F9 AC <font color=red>**1D 03 B9 00**</font> 7F 8F FF 20 ;|

说明判断正确, 修改设置项
|31|30|29|28| |27|26|25|24| |23|22|21|20| |19|18|17|16| |15|14|13|12| |11|10|9|8| |7|6|5|4| |3|2|1|0|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|0|0|0|1| |1|1|<font color=red>**1**</font>|1| |<font color=red>**1**</font>|0|0|0| |0|0|1|1| |1|<font color=red>**1**</font>|1|1| |1|0|0|1| |0|0|0|0| |0|0|0|0|

转换成16进制 `1F83 F900`, 与 `MCX354A-FCC_Ax_56G.bin` 一致
使用 `UltraEdit` 更改 `fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754.bin` 并重命名为 `fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin`
> |fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin|
> |-|
> |000ed760h: 00 01 F9 9C <font color=red>**1F 83 F9 00**</font> 7F 8F FF 20 00 01 F9 A0 ;|
> |000ed770h: 00 8F F0 02 03 8F F0 17 00 01 F9 A4 00 40 00 01 ;|
> |000ed780h: 00 D3 01 FF 00 01 F9 AC <font color=red>**1F 83 F9 00**</font> 7F 8F FF 20 ;|

此时并不能直接刷固件, 因为前面说了还有校验码

校验固件
```PowerShell
C:\Program Files\Mellanox\WinMFT> flint -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin v

     FS2 failsafe image. Start address: 0x0. Chunk size 0x80000:

     NOTE: The addresses below are contiguous logical addresses. Physical addresses on
           flash may be different, based on the image start address and chunk size

     /0x00000038-0x0000065b (0x000624)/ (BOOT2) - OK
     /0x0000065c-0x0000297f (0x002324)/ (BOOT2) - OK
     /0x00002980-0x00003923 (0x000fa4)/ (Configuration) - OK
     /0x00003924-0x00047f5f (0x04463c)/ (ROM) - OK
     /0x00047f60-0x00047fa3 (0x000044)/ (GUID) - OK
     /0x00047fa4-0x0004812f (0x00018c)/ (Image Info) - OK
     /0x00048130-0x00055513 (0x00d3e4)/ (DDR) - OK
     /0x00055514-0x00056577 (0x001064)/ (DDR) - OK
     /0x00056578-0x00056967 (0x0003f0)/ (DDR) - OK
     /0x00056968-0x00094fab (0x03e644)/ (DDR) - OK
     /0x00094fac-0x00099e2f (0x004e84)/ (DDR) - OK
     /0x00099e30-0x0009e423 (0x0045f4)/ (DDR) - OK
     /0x0009e424-0x0009ef1b (0x000af8)/ (DDR) - OK
     /0x0009ef1c-0x000cf0ef (0x0301d4)/ (DDR) - OK
     /0x000cf0f0-0x000d2c9b (0x003bac)/ (DDR) - OK
     /0x000d2c9c-0x000e820f (0x015574)/ (DDR) - OK
     /0x000e8210-0x000e8317 (0x000108)/ (DDR) - OK
     /0x000e8318-0x000ed39b (0x005084)/ (DDR) - OK
     /0x000ed39c-0x000eeb97 (0x0017fc)/ (Configuration) /0x000ed39c/ - wrong CRC (exp:0x8e4c, act:0x9008)
-E- FW image verification failed: Bad CRC.. AN HCA DEVICE CAN NOT BOOT FROM THIS IMAGE.
```
使用 `UltraEdit` 定位到 `0x000eeb96`
> |fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin|
> |-|
> |000eeb90h: 00 00 00 7F 00 00 <font color=red>**90 08**</font> 00 00 00 03 00 00 00 18 ;|

按照提示修改为
> |fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin|
> |-|
> |000eeb90h: 00 00 00 7F 00 00 <font color=red>**8E 4C**</font> 00 00 00 03 00 00 00 18 ;|

现在可以把ini文件再更新进去
复制 `HP_1380110017.ini` 为 `HP_1380110017_56G.ini`, 修改 `[IB]` 部分
```ini
;;speed flags for port0
cx3_spec1_3_ib_support_port0 = 1
cx3_spec1_2_ib_support_port0 = 1
spec1_3_fdr10_ib_support_port0 = 1
spec1_3_fdr14_ib_support_port0 = 1
port1_802_3ap_56kr4_ability = 1
port1_802_3ap_cr4_ability = 1
port1_802_3ap_cr4_enable  = 1

;;speed flags for port1
cx3_spec1_3_ib_support_port1 = 1
cx3_spec1_2_ib_support_port1 = 1
spec1_3_fdr10_ib_support_port1 = 1
spec1_3_fdr14_ib_support_port1 = 1
port2_802_3ap_56kr4_ability = 1
port2_802_3ap_cr4_ability = 1
port2_802_3ap_cr4_enable  = 1
```
改为
```ini
;;speed flags for port0
cx3_spec1_3_ib_support_port0 = 1
cx3_spec1_2_ib_support_port0 = 1
spec1_3_fdr10_ib_support_port0 = 1
spec1_3_fdr14_ib_support_port0 = 1
port1_802_3ap_56kr4_ability = 1
port1_802_3ap_56kr4_enable = 1
port1_802_3ap_cr4_ability = 1
port1_802_3ap_cr4_enable  = 1
port1_802_3ap_kr4_ability = 1
port1_802_3ap_kr4_enable = 1

;;speed flags for port1
cx3_spec1_3_ib_support_port1 = 1
cx3_spec1_2_ib_support_port1 = 1
spec1_3_fdr10_ib_support_port1 = 1
spec1_3_fdr14_ib_support_port1 = 1
port2_802_3ap_56kr4_ability = 1
port2_802_3ap_56kr4_enable = 1
port2_802_3ap_cr4_ability = 1
port2_802_3ap_cr4_enable  = 1
port2_802_3ap_kr4_ability = 1
port2_802_3ap_kr4_enable = 1
```

替换固件内的ini文件
```PowerShell
C:\Program Files\Mellanox\WinMFT> python3 fs2_update_ini.py fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin HP_1380110017_56G.ini
```

再次校验固件
```PowerShell
C:\Program Files\Mellanox\WinMFT> flint -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin v

     FS2 failsafe image. Start address: 0x0. Chunk size 0x80000:

     NOTE: The addresses below are contiguous logical addresses. Physical addresses on
           flash may be different, based on the image start address and chunk size

     /0x00000038-0x0000065b (0x000624)/ (BOOT2) - OK
     /0x0000065c-0x0000297f (0x002324)/ (BOOT2) - OK
     /0x00002980-0x00003923 (0x000fa4)/ (Configuration) - OK
     /0x00003924-0x00047f5f (0x04463c)/ (ROM) - OK
     /0x00047f60-0x00047fa3 (0x000044)/ (GUID) - OK
     /0x00047fa4-0x0004812f (0x00018c)/ (Image Info) - OK
     /0x00048130-0x00055513 (0x00d3e4)/ (DDR) - OK
     /0x00055514-0x00056577 (0x001064)/ (DDR) - OK
     /0x00056578-0x00056967 (0x0003f0)/ (DDR) - OK
     /0x00056968-0x00094fab (0x03e644)/ (DDR) - OK
     /0x00094fac-0x00099e2f (0x004e84)/ (DDR) - OK
     /0x00099e30-0x0009e423 (0x0045f4)/ (DDR) - OK
     /0x0009e424-0x0009ef1b (0x000af8)/ (DDR) - OK
     /0x0009ef1c-0x000cf0ef (0x0301d4)/ (DDR) - OK
     /0x000cf0f0-0x000d2c9b (0x003bac)/ (DDR) - OK
     /0x000d2c9c-0x000e820f (0x015574)/ (DDR) - OK
     /0x000e8210-0x000e8317 (0x000108)/ (DDR) - OK
     /0x000e8318-0x000ed39b (0x005084)/ (DDR) - OK
     /0x000ed39c-0x000eeb97 (0x0017fc)/ (Configuration) - OK
     /0x000eeb98-0x000eec0b (0x000074)/ (Jump addresses) - OK
     /0x000eec0c-0x000ef7d7 (0x000bcc)/ (FW Configuration) - OK
     /0x00000000-0x000ef7d7 (0x0ef7d8)/ (Full Image) - OK

-I- FW image verification succeeded. Image is bootable.
```
全部OK

## 刷入固件
获取网卡名
```PowerShell
C:\Program Files\Mellanox\WinMFT> mst status -v
MST devices:
------------
  mt4103_pci_cr0         bus:dev.fn=2d:00.0
  mt4103_pciconf0        bus:dev.fn=2d:00.0
```

刷入修改好的固件
```PowerShell
C:\Program Files\Mellanox\WinMFT> flint -d mt4103_pci_cr0 -i fw-ConnectX3Pro-rel-2_42_5700-764285-B21_Ax-CLP-8025-UEFI-14.11.49-FlexBoot-3.4.754_56G.bin b

    Current FW version on flash:  2.42.5700
    New FW version:               2.42.5700

    Note: The new FW version is the same as the current FW version on flash.

 Do you want to continue ? (y/n) [n] : y

Burning FS2 FW image without signatures - OK
Restoring signature                     - OK
```

(可选)删除 [FlexBoot](https://network.nvidia.com/products/adapter-software/flexboot/)
```PowerShell
C:\Program Files\Mellanox\WinMFT> flint -d mt4103_pci_cr0 --allow_rom_change drom

-I- Preparing to remove ROM ...
Removing ROM image    - OK
Restoring signature  - OK
```

重置网卡
```PowerShell
C:\Program Files\Mellanox\WinMFT> mlxconfig -d mt4103_pci_cr0 reset

 Reset configuration for device mt4103_pci_cr0? (y/n) [n] : y
Applying... Done!
-I- Please reboot machine to load new configurations.
```

重启即可开启56G

## 参考
- [Mellanox-ConnectX3-MCX353A-QCBT开启56GbE](https://c4c.club/index.php/2020/03/15/mellanox-connectx3-mcx353a-qcbt%e5%bc%80%e5%90%af56gbe)
- [HowTo Setup 56GbE Back-to-Back on two servers](https://enterprise-support.nvidia.com/s/article/howto-setup-56gbe-back-to-back-on-two-servers)
- [Looking for a Connectx-3 custom firmware package](https://forums.servethehome.com/index.php?threads/looking-for-a-connectx-3-custom-firmware-package.18476/)



