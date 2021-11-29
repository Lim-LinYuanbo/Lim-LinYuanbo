---
layout: mypost
title: JLink Commander 常用操作记录
categories: [编码笔记]
---

## Link Commander 常用操作记录

### JLink 安装

主机系统：Ubuntu 20.04.3 LTS

目标主板：[stm32f103战舰开发板](http://www.openedv.com/docs/boards/stm32/zdyz_stm32f103_warship.html)

下载地址：[https://www.segger.com/downloads/jlink/](https://www.segger.com/downloads/jlink/)

```shell
# 下载 & 安装 (略)
# 默认安装路径：/opt/SEGGER
# 连接 JLink V2 之后查看,查看 USB 列表
$ lsusb
Bus 003 Device 016: ID 1366:0105 SEGGER J-Link_J-Link V9.3 Plus
```

### JLinkExe 常用指令与备注

参考：[J-Link Commander](https://wiki.segger.com/J-Link_Commander)

| 命令(long) | 命令(short) | 备注 |
| --- | --- | --- |
| Connect | Con | 连接设备 |
| Device | Device | 选择模板芯片，可选择芯片见`JLinkDevices.xml`文件 |
| SelectInterface | SI | 选择连接接口，例如：SWD，JTAG等等 |
| Speed | Speed | 设置连接速度，单位：KHz |
| Halt | H | 暂停CPU，似乎可以用于已经正常运作的情况 |
| Go | G | Start CPU if halted |
| Reset | R | Reset CPU |
| Step | S | 单步运行，单步运行汇编指令 |
| Regs | Regs | Display CPU register contents |
| RReg | RReg | 读取CPU寄存器，例如：RReg 0，读取R0寄存器 |
| Mem | Mem | 读取内存，例如：Mem 0x8000000 8，读取 0x8000000 8字节 |
| Erase | Erase | 略，`Syntax:Erase [<SAddr>, <EAddr>]` |
| LoadFile | LoadFile | 略，`Syntax:LoadFile <FileName>, [<Addr(.bin only)>]` |
| SaveBin | SaveBin | 略，`Syntax:SaveBin <filename>, <addr>, <NumBytes>` |
| VerifyBin | VerifyBin | 略，`Syntax:VerifyBin <filename>, <addr>` |

### 实操测试记录

1. 连接 J-Link 查看是否连接正常

    ```shell
    # 进入 J-Link Commander
    $ JLinkExe
    
    J-Link>?
    Available commands are:
    ----------------------
    f          Firmware info # 查看固件信息,通过这条指令也可以判断 J-Link 是否连接正常
    hwinfo     Show hardware info # 查看硬件(J-Link)信息
    # 实例
    J-Link>f
    Firmware: J-Link V9 compiled May  7 2021 16:26:12
    Hardware: V9.20
    
    J-Link>hwinfo
    HWInfo[00] = Target power is enabled
    HWInfo[02] = 74mA       (ITarget)
    HWInfo[03] = 328mA      (ITargetPeak)
    HWInfo[04] = 328mA      (ITargetPeakOperation)
    HWInfo[10] = 0ms        (ITargetMaxTime0)
    HWInfo[11] = 0ms        (ITargetMaxTime1)
    HWInfo[12] = 0ms        (ITargetMaxTime2)
    HWInfo[13] = 0x00012189
    ```

2. 连接目标设备

    ```shell
    # 导出支持的设备列表,路径似乎需要使用绝对路径,
    ExpDevList Exports the device names from the DLL internal
               device list to a text file
                 Syntax: ExpDevList <Filename>
    ExpDevListXML Exports the device names from the DLL internal
               device list to a text file in XML format
                 Syntax: ExpDevListXML <Filename>
    # 实例
    J-Link>ExpDevList /home/linyuanbo/device_list
    Opening text file for writing... [/home/linyuanbo/device_list]
    
    # 连接目标设备
    J-Link>connect
    # 选择设备类型,支持的设备可以通过 ExpDevList 导出的文件查看
    Please specify device / core. <Default>: STM32F103ZE
    Type '?' for selection dialog
    Device>STM32F103ZE
    # 选择连接接口
    Please specify target interface:
      J) JTAG (Default)
      S) SWD
      T) cJTAG
    TIF>S
    # 连接速率
    Specify target interface speed [kHz]. <Default>: 4000 kHz
    Speed>
    Device "STM32F103ZE" selected.
    ...
    ```

3. 目标设备控制

    ```shell
    erase      Erase internal flash of selected device. Syntax: Erase # 擦除芯片
    loadfile   Load data file into target memory.
                 Syntax: loadfile <filename>, [<addr>]
                 Supported extensions: *.bin, *.mot, *.hex, *.srec
                 <addr> is needed for bin files only.
    loadbin    Load *.bin file into target memory. # 加载文件
                 Syntax: loadbin <filename>, <addr>
    savebin    Saves target memory into binary file. # 读取文件
                 Syntax: savebin <filename>, <addr>, <NumBytes>
    verifybin  Verfies if the specified binary is already in the target memory at the specified     address. #校验文件
                 Syntax: verifybin <filename>, <addr>
    r          Reset target         (RESET) # 复位芯片
    g          go # 启动
    mem        Read memory. Syntax: mem  [<Zone>:]<Addr>, <NumBytes> (hex) # 读取内存
    q          Quit
    
    # 实例
    J-Link>erase
    Erasing device...
    Comparing flash   [100%] Done.
    Erasing flash     [100%] Done.
    Verifying flash   [100%] Done.
    J-Link: Flash download: Total time needed: 5.714s (Prepare: 0.019s, Compare: 0.000s, Erase: 5.    693s, Program: 0.000s, Verify: 0.000s, Restore: 0.002s)
    Erasing done.
    
    J-Link>loadbin /home/linyuanbo/Desktop/rt-thread/bsp/stm32/stm32f103-atk-warshipv3/rtthread.bin     0x8000000
    Downloading file [/home/linyuanbo/Desktop/rt-thread/bsp/stm32/stm32f103-atk-warshipv3/rtthread.    bin]...
    Comparing flash   [100%] Done.
    Erasing flash     [100%] Done.
    Programming flash [100%] Done.
    Verifying flash   [100%] Done.
    J-Link: Flash download: Bank 0 @ 0x08000000: 1 range affected (67584 bytes)
    J-Link: Flash download: Total time needed: 1.909s (Prepare: 0.046s, Compare: 0.006s, Erase: 0.    000s, Program: 1.828s, Verify: 0.001s, Restore: 0.026s)
    O.K.
    
    J-Link>r
    Reset delay: 0 ms
    Reset type NORMAL: Resets core & peripherals via SYSRESETREQ & VECTRESET bit.
    Reset: Halt core after reset via DEMCR.VC_CORERESET.
    Reset: Reset device via AIRCR.SYSRESETREQ.
    J-Link>g
    J-Link>q
    ```
### 一键烧录脚本

  > 每次敲指令也是麻烦的，把所有指令集合起来，让 JLink 自己运行。
  
  ```jlink
  connect # 连接仿真器
  device N32G452RC # 选择目标芯片
  si SWD # 选择接口方式
  speed 4000 # 选择速率
  h # 暂停CPU
  r # 复位CPU
  erase # 擦除
  
  loadfile rtthread.bin 0x8000000 # 写入 文件 地址
  verifybin rtthread.bin 0x8000000 # 简易 文件 地址
  r # 复位CPU
  go # 启动
  q # 退出
  ```
  
  ```bat
  # JLink ... -CommanderScript 脚本名
  JLink -device N32G452RC -if swd -speed 4000 -CommanderScript program.jlink
  ```