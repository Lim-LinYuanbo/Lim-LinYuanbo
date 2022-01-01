---
#layout: mypost
title: 微型嵌入式实时操作系统
categories:
- [RTOS]
tags:
- RTOS
- 源码阅读
---

### SmallRTOS - Small Real Time Operating System

### 官方地址:[http://www.smallrtos.org/](http://www.smallrtos.org/)

---

说明：

1、Demo代码基于cortex-M3的MCU，所以部分汇报指令与寄存器需要配合《ARM Cortex-M3权威指南》和《Cortex-M3+技术参考手册》才能看懂；

2、SmallRTOSConfig.h - 配置系统基本属性文件，例如：MCU晶振、系统心跳、堆栈大小、任务数量、各类宏开关（信号量、互斥锁等）等等；

3、Fit_Type.h - 系统类型定义文件，包括：堆栈类型、堆栈生长方向、堆栈对齐方式；

4、SR_Global.c - 内核全局变量，包括：当前运行任务句柄、各类句柄；

5、SR_Memory.c - 系统内存资源分配相关（没仔细看）

6、Fit_CPU.c - 与平台相关，没注释的地方不能看懂。

---

其他：

1、系统任务根据不同状态分组（数组形式）；

2、前后任务连接形式-链式；

3、任务句柄空间与堆栈空间分开，句柄中有指向堆栈成员指针；

4、任务切换，任务调度开始与ARM Cortex-M3有莫大的关系，需详细看ARM Cortex-M3权威指南才能理解。

---

备注:

学习版本:SmallRTOSV1.8.0

学习日期:2015.6.3

学习笔记:![草图](small-RTOS.jpg)

学习笔记:![草图](../posts/2019/04/07/small-RTOS.jpg)
