---
#layout: mypost
title: RT-Thread stable-v3.0.x 裁剪笔记
categories:
- [RTOS]
tags:
- RTOS
- RT-Thread
---

测试硬件平台：STM32F103ZET6（正点原子 战舰V3 开发板）

源码版本：RT-Thread stable-v3.0.x

编译环境：RT-THread 官方的 ENV，具体 GCC 版本信息如下：

```shell
> arm-none-eabi-gcc -v
gcc version 5.4.1 20160919 (release) [ARM/embedded-5-branch revision 240496] (GNU Tools for ARM Embedded Processors)
```

### 默认配置

```shell
$ git checkout -b test-cutting origin/stable-v3.0.x
Updating files: 100% (18136/18136), done.
Branch 'test-cutting' set up to track remote branch 'stable-v3.0.x' from 'origin'.
Switched to a new branch 'test-cutting'

> scons
scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
 177844    2708    5124  185676   2d54c rtthread-stm32.elf
scons: done building targets.
```

### 删除 DFS+CAN

```shell
RT-Thread Components  --->
    Device virtual file system  --->
        [ ] Using device virtual file system
    Device Drivers  --->
        [ ] Using CAN device drivers

scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
  76544    1948    3400   81892   13fe4 rtthread-stm32.elf
scons: done building targets.
```

### 删除 FINSH

```shell
RT-Thread Components  --->
    Command shell  --->
        [ ] finsh shell

> scons
scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
  41024    1520    2180   44724    aeb4 rtthread-stm32.elf
scons: done building targets.
```

### 关闭组件自动初始化

```shell
RT-Thread Components  --->
    [ ] Use components automatically initialization

scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
  34036     444    2104   36584    8ee8 rtthread-stm32.elf
scons: done building targets.
```

### 关闭设备模型与调试

```shell
RT-Thread Kernel  --->
    Kernel Device Object  --->
        [ ] Using device object
        [ ] Using console for rt_kprintf
RT-Thread Components  --->
    Device Drivers  --->
        [ ] Using device drivers IPC
        [ ] Using generic GPIO device drivers
[ ] Enable UART1 (PA9/10)
[ ] Enable UART2 (PA2/3)
[ ] Enable UART3 (PB10/11)

# 屏蔽 USART 的调用
$ git diff bsp/stm32f10x/drivers/board.c
-    rt_hw_usart_init();
-    rt_console_set_device(RT_CONSOLE_DEVICE_NAME);
+//    rt_hw_usart_init();
+//    rt_console_set_device(RT_CONSOLE_DEVICE_NAME);
# 屏蔽 USART 驱动编译
$ git diff bsp/stm32f10x/drivers/SConscript
-usart.c
# 使能 LED 灯用于指示程序已经正确运行
-    rt_hw_usart_init();
-    rt_console_set_device(RT_CONSOLE_DEVICE_NAME);
+//    rt_hw_usart_init();
+//    rt_console_set_device(RT_CONSOLE_DEVICE_NAME);

scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
  13272     132    1716   15120    3b10 rtthread-stm32.elf
scons: done building targets.
```

### 关闭一些杂项，其实占用空间很小

```shell
RT-Thread Kernel  --->
    [ ] Enable debug features
    [ ] Enable system hook
    Inter-Thread communication  --->
        [ ] Enable event flag
        [ ] Enable mailbox
         [ ] Enable message queue
RT-Thread Components  --->
    POSIX layer and C standard library  --->
        [ ] Enable libc APIs from toolchain

scons: Reading SConscript files ...
...
LINK rtthread-stm32.elf
arm-none-eabi-objcopy -O binary rtthread-stm32.elf rtthread.bin
arm-none-eabi-size rtthread-stm32.elf
   text    data     bss     dec     hex filename
   9912      84    1636   11632    2d70 rtthread-stm32.elf
scons: done building targets.
```

### 总结

CAN+DFS ：100KB 左右（包含驱动）

finsh shell ： 35KB 左右（包含命令+源码，FINSH 实际大小预计 10KB 左右）

components automatically initialization ： 6KB 左右

device & driver model ： 23KB左右（包括驱动）
