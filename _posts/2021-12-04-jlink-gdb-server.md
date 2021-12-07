---
layout: mypost
title: JLink GDB Server 常用操作记录
categories: [编码笔记]
---

## JLink GDB Server 常用操作记录

### JLink 安装

主机系统：Ubuntu 20.04.3 LTS

目标主板：[stm32f103战舰开发板](http://www.openedv.com/docs/boards/stm32/zdyz_stm32f103_warship.html)

下载地址：[https://www.segger.com/downloads/jlink/](https://www.segger.com/downloads/jlink/)

```shell
# 下载 & 安装 (略)
# 默认安装路径：/opt/SEGGER
# 查看安装文件 GDB 相关程序
$ ls -al JLinkGDBServer*
lrwxrwxrwx 1 root root     19 10月 26  2018 JLinkGDBServer -> JLinkGDBServerCLExe
-rwxr-xr-x 1 root root 589640 10月 26  2018 JLinkGDBServerCLExe
-rwxr-xr-x 1 root root 718072 10月 26  2018 JLinkGDBServerExe # 这玩意目前不知道是干嘛的
# 连接 JLink V2 之后查看,查看 USB 列表
$ lsusb
Bus 003 Device 016: ID 1366:0105 SEGGER J-Link_J-Link V9.3 Plus
```

### JLinkGDBServerCL 常用选项与备注

参考：[J-Link GDB Server Command line options](https://wiki.segger.com/J-Link_GDB_Server#Command_line_options)

| 选项 | 解释 | 备注 |
| --- | --- | --- |
| -device | Selects the connected target device. | 例如：-device STM32F103RC |
| -endian | Selects the device endianness. | 例如：-endian little |
| -if | Selects the interface to connect to the target. | 例如：-if SWD |
| -speed | Selects the target communication speed. | 例如：-speed 4000 |
| -port | Select the port to listen for GDB clients. | 例如：-port 12345 |
| -localhostonly | Allow only localhost connections (Windows default) | Windows 默认只支持本地连接<br>关闭只支持本地连接`-localhostonly 0` |
| -nolocalhostonly | Allow connections from outside localhost (Linux default) | Windows 常用，同`-localhostonly 0` |

```shell
# Windows 常用选项
> .\JLinkGDBServerCL.exe -select USB -device STM32F103RC -endian little -if SWD -speed 4000 -noir -noLocalhostOnly
# Linux 常用选项
$ ./JLinkGDBServerCLExe -device STM32F103RC -endian little -if SWD -speed 4000
```

### JLinkGDBServerCL 常用指令与备注

参考：[Supported remote (monitor) commands](https://wiki.segger.com/J-Link_GDB_Server#Supported_remote_.28monitor.29_commands)

| 命令(long) | 命令(short) | 备注 |
| --- | --- | --- |
| flash erase | Erases the flash memory of the target device. | 使用前需要halt（暂停）CPU |
| go | Starts the target CPU. | 配合setbp（设置断点）使用 |
| halt | Halts the target CPU. | 某些操作需要停止CPU，目前很少使用 |
| memU8 | Reads or writes a byte from/to given address. | 读写地址，似乎只能单字节操作？？？ |
| reg | Reads or writes from/to given register. | 不常用？？？ |
| regs | Reads and displays all CPU registers. | 查看CPU寄存器 |
| reset | Resets and halts the target CPU. | 复位（见实操测试记录） |
| setbp | Sets an instruction breakpoint at a given address. | 设置断点配合go使用 |
| clrbp | Removes an instruction breakpoint. | 没看懂文档？？？ |
| step | Performs one or more single instruction steps. | 单步运行（机器/汇编指令） |

### 实操测试记录

```shell
# 连接目标开发板
$ ./JLinkGDBServerCLExe -device STM32F103RC -endian little -if SWD -speed 4000
SEGGER J-Link GDB Server V6.40 Command Line Version

JLinkARM.dll V6.40 (DLL compiled Oct 26 2018 15:08:28)

Command line: -device STM32F103RC -endian little -if SWD -speed 4000
-----GDB Server start settings-----
GDBInit file:                  none
GDB Server Listening port:     2331
SWO raw output listening port: 2332
Terminal I/O port:             2333
Accept remote connection:      yes
Generate logfile:              off
Verify download:               off
Init regs on start:            off
Silent mode:                   off
Single run mode:               off
Target connection timeout:     0 ms
------J-Link related settings------
J-Link Host interface:         USB
J-Link script:                 none
J-Link settings file:          none
------Target related settings------
Target device:                 STM32F103RC
Target interface:              SWD
Target interface speed:        4000kHz
Target endian:                 little

Connecting to J-Link...
J-Link is connected.
Firmware: J-Link V9 compiled May  7 2021 16:26:12
Hardware: V9.60
S/N: 59610373
Feature(s): RDI, GDB, FlashDL, FlashBP, JFlash
Checking target voltage...
Target voltage: 3.36 V
Listening on TCP/IP port 2331
Connecting to target...Connected to target
Waiting for GDB connection...
```

```shell
# GDB 连接目标
$ /opt/gcc-arm-none-eabi-6_2-2016q4/bin/arm-none-eabi-gdb rt-thread.elf
(gdb) target remote localhost:2331

# 暂停
(gdb) monitor halt

# 擦除
(gdb) monitor flash erase
Flash erase: O.K.

# 加载
(gdb) load
Loading section .text, size 0xde04 lma 0x8000000
Loading section .init, size 0x4 lma 0x800de04
Loading section .fini, size 0x4 lma 0x800de08
Loading section .data, size 0x5a8 lma 0x800de0c
Start address 0x800060c, load size 58292
Transfer rate: 11385 KB/sec, 8327 bytes/write.

# 复位
(gdb) monitor reset
Resetting target

# 查看 CPU 寄存器
(gdb) monitor regs
R0 = 00000000, R1 = 00000000, R2 = 20000D88, R3 = 20000D88
R4 = DEADBEEF, R5 = DEADBEEF, R6 = DEADBEEF, R7 = 20000908
R8 = DEADBEEF, R9 = DEADBEEF, R10= DEADBEEF, R11= DEADBEEF
R12= 00000000, R13= 200007A8, MSP= 200007A8, PSP= 20000908
R14(LR) = FFFFFFFF, R15(PC) = 0800060C
XPSR 01000000, APSR 00000000, EPSR 01000000, IPSR 00000000
CFBP 00000000, CONTROL 00, FAULTMASK 00, BASEPRI 00, PRIMASK 00
# 此时 R15(PC) = 0800060C，反汇编之后查看该地址内容
0800060c <Reset_Handler>:
 800060c:       2100            movs    r1, #0
 800060e:       e003            b.n     8000618 <LoopCopyDataInit>

# 连续执行单步运行之后
(gdb) monitor step
(gdb) monitor step
(gdb) monitor step
(gdb) monitor regs
R0 = 00000000, R1 = 00000000, R2 = 20000D88, R3 = 20000D88
R4 = DEADBEEF, R5 = DEADBEEF, R6 = DEADBEEF, R7 = 20000908
R8 = DEADBEEF, R9 = DEADBEEF, R10= DEADBEEF, R11= DEADBEEF
R12= 00000000, R13= 200007A8, MSP= 200007A8, PSP= 20000908
R14(LR) = FFFFFFFF, R15(PC) = 08000618
XPSR 41000000, APSR 40000000, EPSR 01000000, IPSR 00000000
CFBP 00000000, CONTROL 00, FAULTMASK 00, BASEPRI 00, PRIMASK 00
# 此时 R15(PC) = 0800060C，反汇编之后查看该地址内容
# 确实跳转 LoopCopyDataInit 函数运行
08000618 <LoopCopyDataInit>:
 8000618:       4809            ldr     r0, [pc, #36]   ; (8000640 <LoopFillZerobss+0x14>)
 800061a:       4b0a            ldr     r3, [pc, #40]   ; (8000644 <LoopFillZerobss+0x18>)
 800061c:       1842            adds    r2, r0, r1
 800061e:       429a            cmp     r2, r3
 8000620:       d3f6            bcc.n   8000610 <CopyDataInit>
 8000622:       4a09            ldr     r2, [pc, #36]   ; (8000648 <LoopFillZerobss+0x1c>)
 8000624:       e002            b.n     800062c <LoopFillZerobss>
```