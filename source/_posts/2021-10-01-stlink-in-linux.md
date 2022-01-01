---
#layout: mypost
title: STLink in Linux 常用操作记录
categories:
- [tools]
tags:
- tools
- GDB
---

## STLink in Linux 常用操作记录

### STLink 安装

> 其他安装方式参考[https://github.com/stlink-org/stlink](https://github.com/stlink-org/stlink)

主机系统：Ubuntu 20.04.3 LTS

目标主板：[stm32f103战舰开发板](http://www.openedv.com/docs/boards/stm32/zdyz_stm32f103_warship.html)

```shell
# 使用 apt 直接安装 stlink-tools
$ sudo apt install stlink-tools
# 连接 STLink V2 之后查看,查看 USB 列表
$ lsusb
Bus 003 Device 014: ID 0483:3748 STMicroelectronics ST-LINK/V2
```

### st-info

```shell
# Display amount of flash memory available in the device
# 查看 flash 容量
$ st-info --flash
0x80000

# Display amount of sram memory available in device
# 查看 sram 容量
$ st-info --sram
0x10000

# Display the summarized information of the connected programmers and devices
# 查看概览
$ st-info --probe
Found 1 stlink programmers
 serial: 523f6e06657055511630123f
openocd: "\x52\x3f\x6e\x06\x65\x70\x55\x51\x16\x30\x12\x3f"
  flash: 524288 (pagesize: 2048)
   sram: 65536
 chipid: 0x0414
  descr: F1 High-density device
```

### st-flash

```shell
# 常用操作:写入,读取,擦除,复位
$ man st-flash
COMMANDS
       write FILE ADDR
              Write firmware FILE to device starting from ADDR

       read FILE ADDR SIZE
              Read firmware from device starting from ADDR up to SIZE bytes to FILE

       erase  Perform a mass erasing of the device firmware

       reset  Reset the target
EXAMPLES
       Flash firmware.bin to device

              $ st-flash write firmware.bin 0x8000000

       Read firmware from device (4096 bytes)

              $ st-flash read firmware.bin 0x8000000 4096

       Erase firmware from device

              $ st-flash erase
```

### st-util

```shell
# 最常用的应该只有设置 GDB server 端口
$ man st-util
OPTIONS
       -p 4242, --listen_port=1234
              Set the gdb server listen port.  (default port: 4242)
EXAMPLES
       Run GDB server on port 4500 and connect to it

              $ st-util -p 4500
              $ gdb
              (gdb) target extended-remote localhost:4500
```