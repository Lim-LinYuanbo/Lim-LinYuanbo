---
#layout: mypost
title: RT-Thread N32G452XX 移植笔记
categories:
- [RTOS]
tags:
- RTOS
- RT-Thread
---

## RT-Thread N32G452XX 移植笔记

测试硬件平台：N32G452RC

源码版本：[RT-Thread master](https://github.com/RT-Thread/rt-thread)

长裤地址：[https://github.com/RT-Thread/rt-thread/tree/master/bsp/n32g452xx](https://github.com/RT-Thread/rt-thread/tree/master/bsp/n32g452xx)

工作环境：

    1. Win10 64bit (MDK533)
    2. Ubuntu 20.04 (GCC 6.2.1 20161205 (release))

### 一、环境搭建

#### Win10 64bit

1. 搭建好 N32G452XX 裸机开发环境
2. 下载 RTT ENV，参考[使用 Env 创建 RT-Thread 项目工程](https://www.rt-thread.org/document/site/#/rt-thread-version/rt-thread-standard/application-note/setup/standard-project/an0017-standard-project)

3. 下载 RTT 源码

#### Ubuntu 20.04

1. 下载安装[J-flash](https://www.segger.com/downloads/jlink/)
2. 根据官方教程即可，参考[在 Ubuntu 平台开发 RT-Thread](https://www.rt-thread.org/document/site/#/rt-thread-version/rt-thread-standard/application-note/setup/qemu/ubuntu/an0005-qemu-ubuntu)

3. 下载 RTT 源码

### 二、移植步骤

> 个人思路：
>
> 1. 选择已有并且接近芯片进行移植
> 2. 了解该 BSP 的目录结构与编译流程
> 3. 通过 grep 搜索相关关键词进行替换

#### 1. 对比选择

> 经过对比国产的XXX32，我发现已有的 BSP 中，AT32 的目录和 STM32 比较接近，也比较清晰。

具体芯片对比如下：

| 硬件      | 描述          | 描述          |
| --------- | ------------- | ------------- |
| 芯片型号  | N32G452RCL7  | AT32F407VGT7  |
| CPU       | ARM Cortex M4 | ARM Cortex M4 |
| 主频      | 144M          | 240M          |
| 片内SRAM  | 80K可扩展144K | 96K可扩展224K |
| 片内FLASH | 256K         | 1024K         |

#### 2.BSP 目录结构

    ```shell
    # 目录路径：rt-thread/bsp/at32
    $ tree -d -L 1
    .
    ├── Libraries # 库文件
    ├── at32f403a-start # 具体 芯片 与 BSP 目录
    ├── at32f407-start
    └── tools # 脚本

    # 目录路径：rt-thread/bsp/at32/Libraries
    $ tree -L 2
    .
    ├── AT32_Std_Driver
    │   ├── AT32F4xx_StdPeriph_Driver
    │   ├── CMSIS
    │   └── SConscript # 不知如何描述...你懂的...
    ├── Kconfig # menuconfig 对应的配置文件
    └── rt_drivers
        ├── SConscript # 同上
        ...

    # 目录路径：rt-thread/bsp/at32/at32f407-start
    .
    ├── Kconfig # menuconfig 对应的配置文件
    ├── README.md # 本 BSP 描述文件
    ├── SConscript # 不知如何描述...你懂的...
    ├── SConstruct # 执行 scons 时运行的构建文件
    ├── applications
    │   ├── SConscript # 同上
    │   └── main.c # component init 最后调用的 main 在这里
    ├── board
    │   ├── Kconfig # 同上
    │   ├── SConscript # 同上
    │   ├── board.c # 板子初始化的
    │   ├── board.h # 包含一些 FLASH 与 堆栈 相关大小描述
    │   ├── linker_scripts # 链接文件
    │   │   ├── link.icf # IAR...
    │   │   ├── link.lds # GCC使用的链接文件
    │   │   └── link.sct # ARMCC使用的链接文件
    │   └── msp # MCU Specific Package 可以理解为与具体板子相关的初始化文件
    │       ├── at32_msp.c # 更多的是一些 GPIO 是否复用的初始化
    │       ├── at32_msp.h # 头文件
    │       └── system_at32f4xx.c # 芯片 .s 启动文件调用的始终初始化代码
    ├── rtconfig.h # 执行 menuconfig 读取与生成，以及 RTT 的配置
    ├── rtconfig.py # 包括编译器选择与编译链接相关选项
    ├── template.uvopt # keil 相关不多说
    ├── template.uvoptx # keil 相关不多说
    ├── template.uvproj # keil 相关不多说
    └── template.uvprojx # keil 相关不多说
    ```

#### 3.SCONS 运作文件顺序

    ```shell
    .
    ├── SConscript # 2. 遍历子目录的 SConscript
    # 1. 运行 SConstruct 文件，由于引用 rtconfig.py 所以会看 rtconfig.py 添加的打印
    # 5. 追加完了所有源码(包括Libraries)之后，进行构建
    ├── SConstruct 
    ├── applications
    │   ├── SConscript # 3. 追加 applications 中的源码
    ├── board
    │   ├── SConscript # 4. 追加 board 中的源码
    ├── rtconfig.py
    ├── template.ewd
    ```

个人理解：

1. `rtconfig.py`定义编译器之后，指定了编译选项
2. 通过`SConscript`文件遍历所有需要编译的文件
3. 最后执行`SConstruct`文件中的`DoBuilding(TARGET, objs)`
4. 方法`DoBuilding`位于`RTT_ROOT/tools/building.py`

### 三、开始移植

> 个人认为，这个过程比较简单粗暴，主要分两种情况：
>
> 1. 对于源码文件，`.c`、`.h`和`*.s`直接替换或修改内部与`at32`相关库接口
>
> 2. 对于配置文件，

通过搜索，可以快速检索与`at32`相关的文件，主要修改配置文件如下：

    ```shell
    # 查看 Libraries 与 at32 相关的配置文件
    rt-thread/bsp/at32/Libraries$ grep -irn 'at32' . --exclude "*.c" --exclude "*.h" --exclude-dir=AT32_Std_Driver
    ./Kconfig:1:config SOC_FAMILY_AT32
    ./Kconfig:4:config SOC_SERIES_AT32F403
    ./Kconfig:7:    select SOC_FAMILY_AT32
    ./Kconfig:9:config SOC_SERIES_AT32F413
    ./Kconfig:12:    select SOC_FAMILY_AT32
    ./Kconfig:14:config SOC_SERIES_AT32F415
    ./Kconfig:17:    select SOC_FAMILY_AT32
    ./Kconfig:19:config SOC_SERIES_AT32F403A
    ./Kconfig:22:    select SOC_FAMILY_AT32
    ./Kconfig:24:config SOC_SERIES_AT32F407
    ./Kconfig:27:    select SOC_FAMILY_AT32
    
    # 查看 at32f407-start 与 at32 相关的配置文件
    rt-thread/bsp/at32/at32f407-start$ grep -irn 'at32' . --exclude "*.c" --exclude "*.h"
    ./.config:504:CONFIG_SOC_FAMILY_AT32=y
    ./.config:505:CONFIG_SOC_SERIES_AT32F407=y
    ./.config:510:CONFIG_SOC_AT32F407VGT7=y
    ./board/Kconfig:3:config SOC_AT32F407VGT7
    ./board/Kconfig:5:    select SOC_SERIES_AT32F407
    ./board/linker_scripts/link.lds:2: * linker script for AT32 with GNU ld
    ./board/SConscript:12:msp/at32_msp.c
    ./board/SConscript:13:msp/system_at32f4xx.c
    ./board/SConscript:22:    src += [startup_path_prefix + '/AT32_Std_Driver/CMSIS/AT32/AT32F4xx/src/gcc/startup_at32f407vgt7.s']
    ./board/SConscript:24:    src += [startup_path_prefix + '/AT32_Std_Driver/CMSIS/AT32/AT32F4xx/src/mdk/startup_at32f407vgt7.s']
    ./board/SConscript:26:    src += [startup_path_prefix + '/AT32_Std_Driver/CMSIS/AT32/AT32F4xx/src/iar/startup_at32f407vgt7.s']
    ./board/SConscript:28:CPPDEFINES = ['AT32F407VGT7']
    ./README.md:1:# AT32F407 AT-START 开发板 BSP 说明
    ./README.md:5:AT32F407 AT-START是雅特力推出的一款AT32F407系列的评估板，其搭载的MCU主要资源参数如下：
    ./README.md:9:| 芯片型号  | AT32F407VGT7  |
    ./README.md:17:AT32F407-START板级包支持MDK4﹑MDK5﹑IAR开发环境和GCC编译器，以下是具体版本信息：
    ./README.md:28:- MCU：AT32F407VGT7，主频 240MHz，1024KB FLASH ，96KB可扩展到224KB RAM
    ./README.md:140:AT32F407使用Ethernet且PHY时钟由MCO输出提供时，主频最好采用200Mhz。
    ./SConstruct:49:at32_library = 'AT32_Std_Driver'
    ./SConstruct:50:rtconfig.BSP_LIBRARY_TYPE = at32_library
    ./SConstruct:53:objs.extend(SConscript(os.path.join(libraries_path_prefix, at32_library, 'SConscript')))
    ```

#### 公共库：`Libraries/AT32_Std_Driver`

1. 将`AT32_Std_Driver`改名，个人认为改成`Std_Driver`将更通用
2. 将`AT32_Std_Driver`中`SConscript`的相关依赖修改为正确的路径
3. 将`AT32_Std_Driver`中具体的库替换

#### 公共库：`Libraries/rt_drivers`

1. 将`rt_drivers`中具体驱动根据该芯片的标准库修改
2. 检查`rt_drivers`中`SConscript`是否存在与具体芯片相关的描述

#### 链接脚本`at32f407-start/board/linker_scripts`

1. 主要修改一些`RAM`与`ROM`相关的大小，其他无需修改

#### 板子初始化文件`at32f407-start/board/msp`

1. 将`at32_msp.*`更名，将内容相关的`at32`替换
2. 将`system_at32f4xx.c`替换为对于芯片的具体时钟替换一下就行

#### 其他文件`at32f407-start/board`

1. 将`Kconfig`中的`at32`相关替换一下
2. 将`SConscript`中源码路径替换
3. 将`board.*`中头文件与宏定义替换

#### 关于工程文件

此处使用 keil 打开，重新选择一下芯片类型保存即可

### 四、编译与测试

#### 1. 编译

1. 主要关注缺少了哪些文件与`Sconscript`相关
2. 编译错误，这个就与源码`.c`相关
3. 缺少的定义大多数与`Kconfig`相关

#### 2. 烧录与仿真

1. 首先设置启动文件断点`*.s`，说明程序烧录正确已经从复位开始启动
2. 然后在`int $Sub$$main(void)`中设置断点，确保启动函数已经调用`__main`
3. 最后在`int rtthread_startup(void)`中设置断点，确保各类初始化通过

#### 3. 其他补充

1. 根据原厂FAE提供的信息，芯片掉电会比较慢，所以按复位按键有时无法复位。解决方案：使用keil下载设置下载后复位，使用J-Flash下载也记得设置下载后复位。
