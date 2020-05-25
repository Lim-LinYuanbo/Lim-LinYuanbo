---
layout: mypost
title: Linux 源码阅读
categories: [Linux驱动]
---

<html>
<head>
  <title>Evernote Export</title>
  <basefont face="Tahoma" size="2" />
  <meta http-equiv="Content-Type" content="text/html;charset=utf-8" />
  <meta name="exporter-version" content="YXBJ Windows/308100 (zh-CN, DDL); Windows/6.1.1 (Win64);"/>
  <style>
    body, td {
      font-family: Tahoma;
      font-size: 10pt;
    }
  </style>
</head>
<body>
<a name="2025"/>

# linux/Timer.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/1/3 11:22_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/4/9 16:33_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><font color="#FF0000">struct timer_list</font> {

     /*

     * All fields that change during normal runtime grouped to the

     * same cacheline

     */

     struct list_head entry; // 定时器链表入口

     unsigned long expires; // 以jiffies为单位的过期时间

     struct tvec_base *base; // 定时器内部值，用户不要使用

     <font color="#FF0000">void (*function)(unsigned long);</font> // 定时处理函数（回调）

     <font color="#FF0000">unsigned long data;</font> // 定时处理函数参数

     int slack;

#ifdef CONFIG_TIMER_STATS

     int start_pid;

     void *start_site;

     char start_comm[16];

#endif

#ifdef CONFIG_LOCKDEP

     struct lockdep_map lockdep_map;

#endif

};
<div>
</div><div>
</div><div>#define <font color="#FF0000">init_timer(timer)</font> // 初始化定时器 参数：1-struct timer_list类型变量</div><div>#define <font color="#FF0000">setup_timer(timer, fn, data)</font> // 设置定时器 参数：1-struct timer_list类型变量；2-方法（回调）；3-方法参数</div><div>
</div><div>extern int <font color="#FF0000">del_timer(struct timer_list * timer)</font>; // 在定时过期之前删除定时器 参数：1-struct timer_list类型变量</div><div>extern int <font color="#FF0000">mod_timer(struct timer_list *timer, unsigned long expires)</font>; // 设置定时过期时间，并且期待定时 参数：1-struct timer_list类型变量；2-过期时间（单位：jiffies）</div><div>
</div><div>
</div><div>#ifdef CONFIG_SMP

  extern int <font color="#FF0000">del_timer_sync</font>(struct timer_list *timer); // del_timer的同步版本，目前不太理解，谨慎使用。

#else

# define <font color="#FF0000">del_timer_sync</font>(t)          del_timer(t) // SMP情况下调用上面，非SMP情况下调用这里。

#endif</div></span>
</div>

* * *

<a name="2027"/>

# linux/jiffies.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/1/3 11:38_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/4/9 16:33_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
extern u64 __jiffy_data jiffies_64;

extern unsigned long volatile __jiffy_data <font color="#FF0000">jiffies</font>; // <span style="color: rgb(255, 0, 0);">jiffies - 系统总节拍数目</span>

<div>#define <font color="#FF0000">time_after</font>(a,b)          \ <font color="#FF0000">// 时间前后判断</font> 例：time_after(jiffies, later); // 参数：总节拍数目

     (typecheck(unsigned long, a) &amp;&amp; \

     typecheck(unsigned long, b) &amp;&amp; \

     ((long)(b) - (long)(a) &lt; 0))

#define <font color="#FF0000">time_before</font>(a,b)     time_after(b,a) <span style="color: rgb(255, 0, 0);">// 时间前后判断</span>

#define <font color="#FF0000">time_after_eq</font>(a,b)     \ <span style="color: rgb(255, 0, 0);">// 时间前后判断</span>

     (typecheck(unsigned long, a) &amp;&amp; \

     typecheck(unsigned long, b) &amp;&amp; \

     ((long)(a) - (long)(b) &gt;= 0))

#define <font color="#FF0000">time_before_eq</font>(a,b)     time_after_eq(b,a) <span style="color: rgb(255, 0, 0);">// 时间前后判断</span></div></span>
</div>

* * *

<a name="2062"/>

# linux/input.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/4/6 19:43_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/4/6 21:45_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div align="left"><font face="Tahoma">/*

* Event types

*/

#define</font> <font color="#FF0000" face="Tahoma">EV_SYN            </font> <font face="Tahoma">   0x00

#define</font> <font color="#FF0000" face="Tahoma">EV_KEY            </font> <font face="Tahoma">   0x01

#define</font> <font color="#FF0000" face="Tahoma">EV_REL            </font> <font face="Tahoma">   0x02

#define</font> <font color="#FF0000" face="Tahoma">EV_ABS            </font> <font face="Tahoma">   0x03

#define</font> <font color="#FF0000" face="Tahoma">EV_MSC           </font> <font face="Tahoma">    0x04

#define</font> <font color="#FF0000" face="Tahoma">EV_SW             </font> <font face="Tahoma">  0x05

#define</font> <font color="#FF0000" face="Tahoma">EV_LED            </font> <font face="Tahoma">   0x11

#define</font> <font color="#FF0000" face="Tahoma">EV_SND           </font> <font face="Tahoma">    0x12

#define</font> <font color="#FF0000" face="Tahoma">EV_REP            </font> <font face="Tahoma">   0x14

#define</font> <font color="#FF0000" face="Tahoma">EV_FF              </font> <font face="Tahoma">0x15

#define</font> <font color="#FF0000" face="Tahoma">EV_PWR           </font> <font face="Tahoma">    0x16

#define</font> <font color="#FF0000" face="Tahoma">EV_FF_STATUS </font><font face="Tahoma">         0x17

#define</font> <font color="#FF0000" face="Tahoma">EV_MAX           </font> <font face="Tahoma">    0x1f

#define</font> <font color="#FF0000" face="Tahoma">EV_CNT           </font> <font face="Tahoma">    (EV_MAX+1)</font></div><div align="left"><font face="Tahoma">
</font></div><div align="left">/*

* Absolute axes

*/

#define <font color="#FF0000">ABS_X</font>               0x00

#define <font color="#FF0000">ABS_Y</font>               0x01</div><div align="left">...</div><div align="left"><font face="Tahoma">
</font></div><div align="left"><font color="#FF0000">struct input_dev</font> {

     const char *name; // ？？

     const char *phys; // ？？

     const char *uniq; // ？？

     struct input_id <font color="#FF0000">id</font>; // 输入设备标识ID，用于适配handler，需要手动设置，也可以不设置？？

     unsigned long propbit[BITS_TO_LONGS(INPUT_PROP_CNT)];

     unsigned long <font color="#FF0000">evbit</font>[BITS_TO_LONGS(EV_CNT)]; // 事件类型位图，需要手动设置。

     unsigned long <font color="#FF0000">keybit</font>[BITS_TO_LONGS(KEY_CNT)]; // 按键键值位图，需要手动设置。

     unsigned long <font color="#FF0000">relbit</font>[BITS_TO_LONGS(REL_CNT)]; // 绝对坐标位图，需要手动设置。</div><div align="left">     unsigned long <font color="#FF0000">absbit</font>[BITS_TO_LONGS(ABS_CNT)]; // 相对坐标位图，需要手动设置。

     unsigned long mscbit[BITS_TO_LONGS(MSC_CNT)];

     unsigned long ledbit[BITS_TO_LONGS(LED_CNT)];

     unsigned long sndbit[BITS_TO_LONGS(SND_CNT)];

     unsigned long ffbit[BITS_TO_LONGS(FF_CNT)];

     unsigned long swbit[BITS_TO_LONGS(SW_CNT)];

     unsigned int hint_events_per_packet;

     unsigned int keycodemax; // 设备支持最大按键个数？？

     unsigned int keycodesize; // 每个按键字节大小？？

     void *keycode;

     int (*setkeycode)(struct input_dev *dev,

                 const struct input_keymap_entry *ke,

                 unsigned int *old_keycode);

     int (*getkeycode)(struct input_dev *dev,

                 struct input_keymap_entry *ke);

     struct ff_device *ff;

     unsigned int repeat_key; // 最近一次按键键值，用于连击？？该成员只读？？

     struct timer_list timer; // 自动连击定时器，位于input_register_device初始化，无需手动设置。

     int rep[REP_CNT]; // 自动连击参数？？

     struct input_mt_slot *mt;

     int mtsize;

     int slot;

     int trkid;

     struct input_absinfo *<font color="#FF0000">absinfo</font>; // 用于存储Absolute axes属性？可以通过<span style="text-align: -webkit-auto;">input_set_abs_params/</span><span style="text-align: -webkit-auto;">input_alloc_absinfo分配。</span></div><div align="left">

     unsigned long key[BITS_TO_LONGS(KEY_CNT)]; // 当前KEY状态位图？？

     unsigned long led[BITS_TO_LONGS(LED_CNT)]; // 当前LED状态位图？？

     unsigned long snd[BITS_TO_LONGS(SND_CNT)]; // 当前SOUND状态位图？？</div><div align="left">     unsigned long sw[BITS_TO_LONGS(SW_CNT)]; // 当前SWITCH状态位图？？

     int (*open)(struct input_dev *dev); // 输入设备打开函数？？

     void (*close)(struct input_dev *dev); // 输入设备关闭函数？？

     int (*flush)(struct input_dev *dev, struct file *file); // 输入设备刷新函数？？

     int (*event)(struct input_dev *dev, unsigned int type, unsigned int code, int value); // 输入设备时间处理函数？？

     struct input_handle __rcu *grab;

     spinlock_t event_lock;

     struct mutex mutex;

     unsigned int users;

     bool going_away;

     bool sync;

     struct device dev;

     struct list_head     h_list; // handle链表，通过input_register_device函数匹配对应handler之后调用connect分配获得。

     struct list_head     node; // input_dev链表，通过input_dev_list串联。

};

#define <font color="#FF0000">to_input_dev</font>(d) container_of(d, struct input_dev, dev) // 通过input中的dev得到input地址</div><div>
</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_event</font>); // 报告指定type、code的输入事件</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_alloc_absinfo</font>); // 分配一个struct input_absinfo结构（存在struct input_dev当中）。</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_release_device</font>); // 通常用于handler？实现输入子设备驱动不常用？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_open_device</font>); // 调用struct input_dev中open函数指针，通常用于handler？实现输入子设备驱动不常用？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_flush_device</font>); // 调用struct input_dev中flush函数指针，通常用于handler？实现输入子设备驱动不常用？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_close_device</font>); // 调用struct input_dev中close函数指针，通常用于handler？实现输入子设备驱动不常用？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_get_keycode</font>); // 调用struct input_dev中<span style="text-align: -webkit-left;">setkeycode</span>函数指针，通常用于handler？实现输入子设备驱动不常用？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_set_keycode</font>); // 调用struct input_dev中getkeycode函数指针，通常用于handler？实现输入子设备驱动不常用？</div><div>
</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_set_abs_params</font>); // 设置<span style="text-align: -webkit-left;">Absolute axes</span>属性？通常用于触摸屏？</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_allocate_device</font>); // 分配一个设备结构</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_free_device</font>); // 释放一个设备结构</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_register_device</font>); // 注册设备</div><div>EXPORT_SYMBOL(<font color="#FF0000">input_unregister_device</font>); // 注销设备</div><div>static inline void <font color="#FF0000">input_report_key</font>(struct input_dev *dev, unsigned int code, int value) // 报告键值
</div><div>static inline void <font color="#FF0000">input_report_rel</font>(struct input_dev *dev, unsigned int code, int value) // 报告相对坐标</div><div>static inline void <font color="#FF0000">input_report_abs</font>(struct input_dev *dev, unsigned int code, int value) // 报告绝对坐标
</div><div>static inline void <font color="#FF0000">input_sync</font>(struct input_dev *dev) // 报告同步事件</div></span>
</div>

* * *

<a name="2064"/>

# linux/bitops.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/4/9 16:33_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/4/9 17:18_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
#define <font color="#FF0000">BIT</font>(nr)               (1UL &lt;&lt; (nr))

#define <font color="#FF0000">BIT_MASK</font>(nr)          (1UL &lt;&lt; ((nr) % BITS_PER_LONG))

#define <font color="#FF0000">BIT_WORD</font>(nr)          ((nr) / BITS_PER_LONG)

#define BITS_PER_BYTE          8

#define <font color="#FF0000">BITS_TO_LONGS</font>(nr)     DIV_ROUND_UP(nr, BITS_PER_BYTE * sizeof(long))
</span>
</div>

* * *

<a name="2076"/>

# asm/bitops.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/4/9 17:24_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/4/9 17:24_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
#define <font color="#FF0000">set_bit</font>(nr,p)               ATOMIC_BITOP(set_bit,nr,p)

#define <font color="#FF0000">clear_bit</font>(nr,p)               ATOMIC_BITOP(clear_bit,nr,p)

#define <font color="#FF0000">change_bit</font>(nr,p)          ATOMIC_BITOP(change_bit,nr,p)

#define <font color="#FF0000">test_and_set_bit</font>(nr,p)          ATOMIC_BITOP(test_and_set_bit,nr,p)

#define <font color="#FF0000">test_and_clear_bit</font>(nr,p)     ATOMIC_BITOP(test_and_clear_bit,nr,p)

#define <font color="#FF0000">test_and_change_bit</font>(nr,p)     ATOMIC_BITOP(test_and_change_bit,nr,p)
<div>
</div><div>#ifndef __ARMEB__

/*

* These are the little endian, atomic definitions.

*/

#define find_first_zero_bit(p,sz)     _find_first_zero_bit_le(p,sz)

#define find_next_zero_bit(p,sz,off)     _find_next_zero_bit_le(p,sz,off)

#define find_first_bit(p,sz)          _find_first_bit_le(p,sz)

#define find_next_bit(p,sz,off)          _find_next_bit_le(p,sz,off)

#else

/*

* These are the big endian, atomic definitions.

*/

#define find_first_zero_bit(p,sz)     _find_first_zero_bit_be(p,sz)

#define find_next_zero_bit(p,sz,off)     _find_next_zero_bit_be(p,sz,off)

#define find_first_bit(p,sz)          _find_first_bit_be(p,sz)

#define find_next_bit(p,sz,off)          _find_next_bit_be(p,sz,off)

#endif
</div></span>
</div>

* * *

<a name="2088"/>

# 杂

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/4/10 23:04_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/13 19:56_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>1、获得.config方式</div><div>1.1 通过 make xxx_defconfig 命令通过读取 .\arch\arm\configs\xxx_defconfig 获得 .\.config</div><div>2.2 通过 make menuconfig 命令通过读取 arch\$ARCH\Kconfig 和 ... 等文件生成界面配置之后获得 .\.config</div><div>
</div><div>2、通过.config编译</div><div>通过 make 命令执行顶层 makefile 读取 .\.config 文件获得 .\include\linux\autoconf.h 和 .\include\config\auto.conf</div></span>
</div>

* * *

<a name="2130"/>

# linux/kobject.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/5/21 14:36_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/2 14:41_</td></tr>
<tr><td>**来源：**</td><td><a href="http://blog.csdn.net/qb_2008/article/details/6846779">_http://blog.csdn.net/qb_2008/article/details/6846779_</a></td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>// **总结：使用kobject、kset时候，脑海必须时刻浮现kobject、kset模型图像，了解他们之间关系。**</div><div>// **通过example_kobject.c、example_kset.c可以快速学习与使用kobject、kset。**</div><div>**
**</div><div>// **API总结：**</div><div>// **1、kobject_add、kobject_init_and_add、kobject_create_and_add、kset_register****、**</div><div>// **<font color="#FF0000">kset_create_and_add </font>****会调 create_dir****函数，从而创建目录；**</div><div><font color="#FF0000"><font color="#FF0000">
</font></font></div><font color="#FF0000">struct kobject</font> {                            // 必须动态申请，当计数量为零时自动释放，通常嵌入其他更大结构，很少单独使用

     const char          *name;                                   // 名字

     struct list_head     entry;                                    // 用于kobject所属kset下的子kobject链表

     struct kobject          *parent;                             // 指向kobject的父节点
<div>     struct kset          *kset;                                     // 指向kobject所属的kset

     struct kobj_type     *ktype;                                // 定义了kobject所属的类型

     struct sysfs_dirent     *sd;                                 // 指向kobject对应的sysfs目录

     struct kref          kref;                                       // 记录kobject的引用计数

     unsigned int state_initialized:1;

     unsigned int state_in_sysfs:1;

     unsigned int state_add_uevent_sent:1;

     unsigned int state_remove_uevent_sent:1;

     unsigned int uevent_suppress:1;

};</div><div>
</div><div>static int populate_dir(struct kobject *kobj);             // 内部函数根据kobj-&gt;ktype-&gt;default_attrs创建文件</div><div>static int create_dir(struct kobject *kobj);                // 内部函数根据kobj创建目录与文件（内调populate_dir）</div><div>static int get_kobj_path_length(struct kobject *kobj); // 内部函数根据kobj获取绝对路径字符长度</div><div>static void fill_kobj_path(struct kobject *kobj, char *path, int length); // 内部函数根据kobj获取绝对路径</div><div>static void kobject_init_internal(struct kobject *kobj);// 初始化kobj成员entry、kref、位领成员</div><div>EXPORT_SYMBOL(<font color="#FF0000">kobject_init</font>);                               // 初始化kobj成员ktype、内调kobject_init_internal</div><div>EXPORT_SYMBOL(<font color="#FF0000">kobject_add</font>);                              // 初始化kobj成员（内调kobject_init_internal）</div><div>                                                                            // 并确定关联parent（但是没有关联kset！！）</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">kobject_init_and_add</font>);          // 初始化kobj成员（内调kobject_init_internal）</div><div>                                                                            // 并添加入kset（在其parent已经确定情况下！！）</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">kobject_create_and_add</font>);     // 申请空间，1-内调kobject_init指定ktype为dynamic_kobj_ktype，</div><div>                                                                            // 2-内调kobject_add确定parent，学习测试非常实用！！</div><div>EXPORT_SYMBOL(<font color="#FF0000">kobject_del</font>);                               // 删除kobj（会不会自动释放空间？？）</div><div>EXPORT_SYMBOL(<font color="#FF0000">kobject_set_name</font>);                     // 设置名字（内部自动申请释放存储名字空间）</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">kobject_rename</font>);                 // 更改名字，同时还向用户通知这一消息？？</div><div>static inline const char *<font color="#FF0000">kobject_name</font>(const struct kobject *kobj); // 获取名字</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">kobject_get_path</font>);               // 获取完整路径（内调kzalloc、get_kobj_path_length和fill_kobj_path）</div><div>EXPORT_SYMBOL(<font color="#FF0000">kobject_get</font>);                              // 引用计数量加一，返回指针对象

EXPORT_SYMBOL(<font color="#FF0000">kobject_put</font>);                              // 引用计数量减一，计数为0时</div><div>                                                                            // 调用release方法释放kobject与name的空间
</div><div>static ssize_t kobj_attr_show(struct kobject *kobj, struct attribute *attr,

                     char *buf);</div><div>static ssize_t kobj_attr_store(struct kobject *kobj, struct attribute *attr,

                      const char *buf, size_t count);</div><div>const struct sysfs_ops kobj_sysfs_ops = {                // 系统默认attr函数，类型：struct sysfs_ops

     .show     = kobj_attr_show,

     .store     = kobj_attr_store,

};</div><div>static struct kobj_type dynamic_kobj_ktype = {         // 使用kobject_create函数默认赋值kobj-&gt;ktype

     .release     = dynamic_kobj_release,

     .sysfs_ops     = &amp;kobj_sysfs_ops,

};</div><div>
</div><div><font color="#FF0000">struct kobj_type</font> {                                                  // 定义了kobject的公共类型，包含操作函数和共有属性

     void (*release)(struct kobject *kobj);

     const struct sysfs_ops *sysfs_ops;                     // 定义了读写属性文件时调用的函数

     struct attribute **default_attrs;                         // 定义了这类kobject公共属性

     const struct kobj_ns_type_operations *(*child_ns_type)(struct kobject *kobj);

     const void *(*namespace)(struct kobject *kobj);

};</div><div>
</div><div><font color="#FF0000">struct kobj_attribute</font> {

     struct attribute attr;                                          // 公共属性

     ssize_t (*show)(struct kobject *kobj, struct kobj_attribute *attr,

               char *buf);

     ssize_t (*store)(struct kobject *kobj, struct kobj_attribute *attr,

               const char *buf, size_t count);

};</div><div>
</div><div><font color="#FF0000">struct kset</font> {

     struct list_head list;              // 所属kobject的链表头

     spinlock_t list_lock;               // 链表访问锁

     struct kobject kobj;               // kset内部的kobject，要表现为sysfs中的目录就必须拥有kobject的功能

     const struct kset_uevent_ops *uevent_ops;

};</div><div>
</div><div>extern void kset_init(struct kset *kset);               // 初始化kset成员list、list_lock、kobject（内调kobject_init_internal）</div><div>EXPORT_SYMBOL(<font color="#FF0000">kset_register</font>);                       // 把kset-&gt;kobj接入系统，发布KOBJ_ADD消息（首先确保设置</div><div>                                                                      // kset-&gt;kobj.name、kset-&gt;kobj.parent、kset-&gt;kobj.kset）

EXPORT_SYMBOL(<font color="#FF0000">kset_unregister</font>);                   // 看源码，很简单。
</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">kset_create_and_add</font>);   // kset快速创建函数，学习测试非常实用！！</div><div>static void kset_release(struct kobject *kobj);    // 释放kset函数，内部函数，供kset_type调用</div><div>static struct kobj_type kset_ktype = {                // 使用kset_create函数默认赋值kset-&gt;kobj.ktype

     .sysfs_ops     = &amp;kobj_sysfs_ops,

     .release = kset_release,

};</div><div>
</div><div>/* The global /sys/kernel/ kobject for people to chain off of */

extern struct kobject *<font color="#FF0000">kernel_kobj</font>;

/* The global /sys/kernel/mm/ kobject for people to chain off of */

extern struct kobject *<font color="#FF0000">mm_kobj</font>;

/* The global /sys/hypervisor/ kobject for people to chain off of */

extern struct kobject *<font color="#FF0000">hypervisor_kobj</font>;

/* The global /sys/power/ kobject for people to chain off of */

extern struct kobject *<font color="#FF0000">power_kobj</font>;

/* The global /sys/firmware/ kobject for people to chain off of */

extern struct kobject *<font color="#FF0000">firmware_kobj</font>;</div><div>
</div></span>
</div>

* * *

<a name="2138"/>

# linux/kref.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/5/21 15:35_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/5/21 15:46_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>/******************</div><div> *kref是一个引用计数器，它被嵌套进其它的结构中，记录所嵌套结构的引用</div><div> *计数，并在计数清零时调用相应的清理函数。</div><div> *****************/</div><div><font color="#FF0000">struct kref</font> {

     atomic_t refcount;

};
<div>
</div><div>EXPORT_SYMBOL(<font color="#FF0000">kref_init</font>);          // 计数量初始化为一

EXPORT_SYMBOL(<font color="#FF0000">kref_get</font>);          // 计数量加一

EXPORT_SYMBOL(<font color="#FF0000">kref_put</font>);          // 计数量减一

EXPORT_SYMBOL(<font color="#FF0000">kref_sub</font>);          // 计数量减N</div></div></span>
</div>

* * *

<a name="2150"/>

# linux/sysfs.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/5/22 17:12_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/6/2 20:53_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><font color="#FF0000">struct attribute</font> {                              // 通用属性结构，通常内嵌到更大的属性中，比如：struct kobj_attribute

     const char          *name;             // 文件名字

     mode_t               mode;              // 文件权限

#ifdef CONFIG_DEBUG_LOCK_ALLOC

     struct lock_class_key     *key;

     struct lock_class_key     skey;

#endif

};
<div>
</div><div><font color="#FF0000">struct attribute_group</font> {                                             // 通用属性集合，似乎需要配合kobject使用？

     const char          *name;                                      // 貌似指针为空没有名字？

     mode_t               (*is_visible)(struct kobject *,

                               struct attribute *, int);

     struct attribute     **attrs;                                    // 目前只是看见对该成员赋值

};</div><div>
</div><div>#define <font color="#FF0000">__ATTR</font>(_name,_mode,_show,_store) { \       // 赋值宏，一般赋值此类结构，比如：struct kobj_attribute

     .attr = {.name = __stringify(_name), .mode = _mode },     \

     .show     = _show,                         \

     .store     = _store,                         \

}</div><div>
</div><div>#define <font color="#FF0000">__ATTR_RO</font>(_name) { \                               // 赋值宏，read only版本

     .attr     = { .name = __stringify(_name), .mode = 0444 },     \

     .show     = _name##_show,                         \

}</div><div>
</div><div><font color="#FF0000">struct bin_attribute</font> {                                               // 专为二进制属性文件设计，在sysfs中表现为二进制文件

     struct attribute     attr;                                       // 通用属性

     size_t               size;

     void               *private;

     ssize_t (*read)(struct file *, struct kobject *, struct bin_attribute *,

               char *, loff_t, size_t);

     ssize_t (*write)(struct file *,struct kobject *, struct bin_attribute *,

               char *, loff_t, size_t);

     int (*mmap)(struct file *, struct kobject *, struct bin_attribute *attr,

              struct vm_area_struct *vma);

};</div><div>
</div><div>struct <font color="#FF0000">sysfs_ops</font> {                                                   // 被struct kobj_type包含

     ssize_t     (*show)(struct kobject *, struct attribute *,char *);

     ssize_t     (*store)(struct kobject *,struct attribute *,const char *, size_t);

};</div><div>
</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_schedule_callback</font>);</div><div>
</div><div>**<font color="#FF0000">注意：sysfs_xxx_dir的函数似乎没有EXPORT，所以不能直接调用，仅供阅读而已。</font>**</div><div>int __must_check <font color="#FF0000">sysfs_create_dir</font>(struct kobject *kobj);    // 创建一个kobject对应的目录，名字kobj-&gt;name</div><div>void <font color="#FF0000">sysfs_remove_dir</font>(struct kobject *kobj);                     // 删除kobj对应目录以及其子目录与文件</div><div>int __must_check <font color="#FF0000">sysfs_rename_dir</font>(struct kobject *kobj, const char *new_name);      // kobj改名

int __must_check <font color="#FF0000">sysfs_move_dir</font>(struct kobject *kobj,                                              // kobj移动至new_parent_kobj

                    struct kobject *new_parent_kobj);</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_get_dirent</font>);                        // 增加目录parent_sd中名为name文件或目录的引用计数</div><div>
</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_create_file</font>);                                                                // 在kobj对应目录创建attr属性文件</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_create_files</font>);</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_chmod_file</font>);</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_file</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_files</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_create_bin_file</font>);                                                          // ...二进制...

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_bin_file</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_create_link</font>);                                                               // ...软链接...

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_link</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_rename_link</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_create_group</font>);                                                            // ...属性集合...</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_update_group</font>);</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_group</font>);
<div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_add_file_to_group</font>);</div>
EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_remove_file_from_group</font>);</div><div>
</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_notify_dirent</font>);</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_notify</font>);</div><div>

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_get</font>);                    // 增加文件或目录引用计数

EXPORT_SYMBOL_GPL(<font color="#FF0000">sysfs_put</font>);                    // 减少...，同时减少上层引用计数，为零时删除响应文件或目录</div><div>int __must_check sysfs_init(void);                     // 系统初始化时调用</div></span>
</div>

* * *

<a name="2244"/>

# linux/klist.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/6/18 11:53_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/6/18 15:26_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>// 链表表头</div>
struct klist_node;
<font color="#FF0000">struct klist</font> {

     spinlock_t          k_lock;

     struct list_head     k_list;

     void               (*get)(struct klist_node *);

     void               (*put)(struct klist_node *);

} __attribute__ ((aligned (sizeof(void *))));
<div>
</div><div>// 链表节点</div><div>// 特别说明:void *n_klist; // 指向表头且第0位表示是否该节点已被请求删除,1-死亡,0-仍可使用</div><div><div><font color="#FF0000">struct klist_node</font> {

     void               *n_klist;     /* never access directly */

     struct list_head     n_node;

     struct kref          n_ref;

};</div>

// 静态初始化struct klist结构

#define <font color="#FF0000">KLIST_INIT</font>(_name, _get, _put)                         \

     { .k_lock     = __SPIN_LOCK_UNLOCKED(_name.k_lock),          \

       .k_list     = LIST_HEAD_INIT(_name.k_list),               \

       .get          = _get,                              \

       .put          = _put, }

// 静态定义并初始化struct klist结构

#define <font color="#FF0000">DEFINE_KLIST</font>(_name, _get, _put)                         \

     struct klist _name = KLIST_INIT(_name, _get, _put)

// 动态初始化klist结构

EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_init</font>);</div><div>
</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_add_head</font>);                      // 从头添加</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_add_tail</font>);                        // 从尾添加

EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_add_after</font>);                     // 从特定节点前面添加</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_add_before</font>);                  // 从特定节点后面添加
<font color="#FF0000">
</font>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_del</font>);                              // <font color="#FF0000">
</font><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_remove</font>);                       // 删除节点</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_node_attached</font>);            //</div><div>
</div></div><div>
</div><div><font color="#FF0000">struct klist_iter</font> {

     struct klist          *i_klist;

     struct klist_node     *i_cur;

};</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_iter_init_node</font>);              // 初始化klist_iter指向klist和klist_node</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_iter_init</font>);                      // 初始化klist_iter指向klist</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_iter_exit</font>);                     // 断开klist_iter所指klist_node</div><div>EXPORT_SYMBOL_GPL(<font color="#FF0000">klist_next</font>);                          // 寻找下一个存活节点???
</div><div>
</div><div>
</div></span>
</div>

* * *

<a name="2269"/>

# /drivers/base/core.c

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/6/27 19:27_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/17 16:32_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>
</div><div>int (*platform_notify)(struct device *dev) = NULL;

int (*platform_notify_remove)(struct device *dev) = NULL;</div><div>
</div><div>
</div><div>static struct kobject *<font color="#FF0000">dev_kobj</font>;                  // 创建/sys/dev目录的kobject

struct kobject *<font color="#FF0000">sysfs_dev_char_kobj</font>;          // 创建/sys/dev/char目录的kobject

struct kobject *<font color="#FF0000">sysfs_dev_block_kobj</font>;         // 创建/sys/dev/block目录的kobject</div><div>struct kset *<font color="#FF0000">devices_kset</font>;                          // 创建/sys/devices目录的kset</div><div>// 创建目录位于devices_init,调用源码:</div><div>// devices_kset = kset_create_and_add(&quot;devices&quot;, &amp;device_uevent_ops, NULL);</div><div>// dev_kobj = kobject_create_and_add(&quot;dev&quot;, NULL);</div><div>// sysfs_dev_block_kobj = kobject_create_and_add(&quot;block&quot;, dev_kobj);</div><div>// sysfs_dev_char_kobj = kobject_create_and_add(&quot;char&quot;, dev_kobj);</div><div>
</div><div>
</div><div>const char *<font color="#FF0000">dev_driver_string</font>(const struct device *dev);</div><div>// 返回设备名字,优先级:drv-&gt;name,dev-&gt;bus-&gt;name,dev-&gt;class-&gt;name</div><div>int <font color="#FF0000">dev_set_name</font>(struct device *dev, const char *fmt, ...);</div><div>// 设置dev名字,实际设置dev-&gt;kobj名字</div><div>struct device *<font color="#FF0000">get_device</font>(struct device *dev);</div><div>void <font color="#FF0000">put_device</font>(struct device *dev);</div><div>
</div><div>
</div><div>static ssize_t dev_attr_show(struct kobject *kobj, struct attribute *attr,

                    char *buf);
</div><div>static ssize_t dev_attr_store(struct kobject *kobj, struct attribute *attr,

                     const char *buf, size_t count);
</div><div>static const struct sysfs_ops dev_sysfs_ops = {

     .show     = dev_attr_show,

     .store     = dev_attr_store,

};</div><div>static void device_release(struct kobject *kobj);
</div><div>static const void *device_namespace(struct kobject *kobj);
</div><div>static struct kobj_type <font color="#FF0000">device_ktype</font> = {

     .release     = device_release,

     .sysfs_ops     = &amp;dev_sysfs_ops,

     .namespace     = device_namespace,

};</div><div>// 创建device之后device_initialize中dev-&gt;kobj默认使用的ktype(源码:kobject_init(&amp;dev-&gt;kobj, &amp;device_ktype);)</div><div>// 每次访问device目录内部属性均通过dev_attr_show和dev_attr_store调用</div><div>
</div><div>
</div><div>const char *device_get_devnode(struct device *dev,

                      mode_t *mode, const char **tmp);</div><div>// 返回设备路径名,不过似乎可以通过device_type或者class定义一些独特返回名称</div><div>static int dev_uevent_filter(struct kset *kset, struct kobject *kobj);
</div><div>// 是否过滤需要判断kobj-&gt;ktype与默认device_ktype是否相同</div><div>static const char *dev_uevent_name(struct kset *kset, struct kobject *kobj);
</div><div>static int dev_uevent(struct kset *kset, struct kobject *kobj,

                struct kobj_uevent_env *env);
</div><div>static const struct kset_uevent_ops <font color="#FF0000">device_uevent_ops</font> = {

     .filter =     dev_uevent_filter,

     .name =          dev_uevent_name,

     .uevent =     dev_uevent,

};</div><div>// 通知用户空间回调函数 filter-过滤 name-返回kset名字 uevent-通过参数返回增加的环境变量</div><div>// 每次操作(操作包括：添加 删除 更改 等等)devices_kset均会触发函数,位于调用</div><div>// 调用源码:devices_kset = kset_create_and_add(&quot;devices&quot;, &amp;device_uevent_ops, NULL);</div><div>// 以上这句话同时创建sys/device目录,管理目录为devices_kset</div><div>
</div><div>
</div><div>static ssize_t show_uevent(struct device *dev, struct device_attribute *attr,

                  char *buf);</div><div>static ssize_t store_uevent(struct device *dev, struct device_attribute *attr,

                   const char *buf, size_t count);</div><div>static struct device_attribute <font color="#FF0000">uevent_attr</font> =

     __ATTR(<font color="#FF0000">uevent</font>, S_IRUGO | S_IWUSR, show_uevent, store_uevent);</div><div>// 创建devices之后默认自带的默认属性,文件名uevent,位于device_add调用</div><div>// 调用:error = device_create_file(dev, &amp;uevent_attr);</div><div>// 例如:sys/devices/virtual/misc/pwm/<font color="#FF0000">uevent</font></div><div>
</div><div>
</div><div>static ssize_t show_dev(struct device *dev, struct device_attribute *attr,

               char *buf);
</div><div>static struct device_attribute <font color="#FF0000">devt_attr</font> =

     __ATTR(<font color="#FF0000">dev</font>, S_IRUGO, show_dev, NULL);</div><div>// 创建devices之后如果dev-&gt;devt存在则创建这个显示设备号的属性,文件名dev,位于device_add调用</div><div>// 调用:error = device_create_file(dev, &amp;devt_attr);</div><div>// 例如:sys/devices/virtual/misc/pwm/<font color="#FF0000">dev</font></div><div>
</div><div>
</div><div>int device_create_file(struct device *dev,

                 const struct device_attribute *attr);                            // 添加dev一个普通文件属性</div><div>void device_remove_file(struct device *dev,

               const struct device_attribute *attr);                              // 删除dev一个普通文件属性</div><div>int device_create_bin_file(struct device *dev,

                  const struct bin_attribute *attr);                               // 添加dev一个二进制文件属性</div><div>void device_remove_bin_file(struct device *dev,

                   const struct bin_attribute *attr);                              // 删除dev一个二进制文件属性</div><div>static int device_add_attributes(struct device *dev,

                    struct device_attribute *attrs);                               // 添加dev多个普通文件属性</div><div>static void device_remove_attributes(struct device *dev,

                         struct device_attribute *attrs);                          // 删除dev多个普通文件属性</div><div>static int device_add_bin_attributes(struct device *dev,

                         struct bin_attribute *attrs);                               // 添加dev多个二进制文件属性</div><div>static void device_remove_bin_attributes(struct device *dev,

                         struct bin_attribute *attrs);                               // 删除dev多个二进制文件属性</div><div>static int device_add_groups(struct device *dev,

                    const struct attribute_group **groups);                 // 添加dev一组普通文件属性集合</div><div>static void device_remove_groups(struct device *dev,

                    const struct attribute_group **groups);                 // 删除dev一组普通文件属性集合</div><div>static int <font color="#FF0000">device_add_attrs</font>(struct device *dev);</div><div>static void <font color="#FF0000">device_remove_attrs</font>(struct device *dev);</div><div>// device_add_attr和device_remove_attrs均调用其上函数实现增加dev-&gt;kobj属性</div><div>// 增加属性包括(如果存在class或type或自带attr_gourp):</div><div>// class:dev-&gt;class-&gt;dev_attrs 和 dev-&gt;class-&gt;dev_bin_attrs</div><div>// type:dev-&gt;type-&gt;groups</div><div>// 自带attr_gourp:dev-&gt;groups</div><div>
</div><div>
</div><div><strike>int device_schedule_callback_owner(struct device *dev,

          void (*func)(struct device *), struct module *owner);</strike></div><div><strike>// 这里没有调用,所有划掉了.</strike></div><div>
</div><div>
</div><div>// virtual_device_parent函数功能:如果/sys/devices没有virtual目录则创建并返回其kobj</div><div>static struct kobject *virtual_device_parent(struct device *dev)</div><div>static void class_dir_release(struct kobject *kobj);</div><div>static const

struct kobj_ns_type_operations *class_dir_child_ns_type(struct kobject *kobj);</div><div>static struct kobj_type class_dir_ktype = {

     .release     = class_dir_release,

     .sysfs_ops     = &amp;kobj_sysfs_ops,

     .child_ns_type     = class_dir_child_ns_type

};</div><div>static struct kobject *

class_dir_create_and_add(struct class *class, struct kobject *parent_kobj);</div><div>static struct kobject *<font color="#FF0000">get_device_parent</font>(struct device *dev,

                         struct device *parent);</div><div>// 根据一系列规则返回dev的父kobj,个人感觉比较复杂,但是只有四种情况?目前不确定</div><div>// 1.传入参数parent-&gt;kobj</div><div>// 2.传入参数...</div><div>// 3.传入参数...</div><div>// 4.NULL</div><div>static void <font color="#FF0000">setup_parent</font>(struct device *dev, struct device *parent);</div><div>// 通过调用传入dev和parent确定赋值dev-&gt;kobj-&gt;parent,具体调用源码:</div><div>// kobj = get_device_parent(dev, parent);</div><div>// if (kobj) dev-&gt;kobj.parent = kobj;</div><div>static void cleanup_glue_dir(struct device *dev, struct kobject *glue_dir);
</div><div>static void cleanup_device_parent(struct device *dev);</div><div>// 这两个函数似乎与setup_parent功能相反</div><div>
</div><div>static int <font color="#FF0000">device_add_class_symlinks</font>(struct device *dev);
</div><div>static void <font color="#FF0000">device_remove_class_symlinks</font>(struct device *dev);</div><div>// 创建/删除 这个dev-&gt;kobj目录下名为&quot;subsystem&quot;指向这个dev-&gt;class-&gt;p-&gt;subsys.kobj的软链接</div><div>// 例如:/sys/devices/virtual/misc/pwm/<font color="#5DDAFF">subsystem</font> ---&gt; /sys/class/misc</div><div>// 创建/删除 这个dev-&gt;class-&gt;p-&gt;subsys.kob目录下名为&quot;与device同名&quot;指向这个dev-&gt;kobj的软链接</div><div>// 例如:/sys/class/misc/<font color="#5DDAFF">pwm</font> ---&gt; /sys/devices/virtual/misc/pwm</div><div>// 如果dev-&gt;parent存在 则有dev-&gt;kobj目录下名为&quot;device&quot;指向dev-&gt;parent-&gt;kobj的软链接</div><div>// 例如:/sys/devices/virtual/input/input0/mouse0/<font color="#5DDAFF">device</font> ---&gt; /sys/devices/virtual/input/input0</div><div>// 最终位于device_add调用,创建每个device都会在sys/...中创建这些软链接</div><div>
</div><div>static struct kobject *device_to_dev_kobj(struct device *dev);</div><div>static int <font color="#FF0000">device_create_sys_dev_entry</font>(struct device *dev);</div><div>static void <font color="#FF0000">device_remove_sys_dev_entry</font>(struct device *dev);</div><div>// 创建/删除 这个device_to_dev_kobj(dev)目录下名为&quot;MAJOR:MINOR&quot;指向这个dev-&gt;kobj的软链接</div><div>// 你会看到sys/dev/char有很多&quot;MAJOR:MINOR&quot;软链接,原因由上,以上又device_add调用</div><div>// 例如:/sys/dev/char/<font color="#5DDAFF">10:130</font> ---&gt; /sys/devices/virtual/misc/watchdog</div><div>
</div><div>static void klist_children_get(struct klist_node *n);
</div><div>static void klist_children_put(struct klist_node *n);
</div><div>int <font color="#FF0000">device_private_init</font>(struct device *dev);</div><div>// 申请分配struct device_private空间,赋值dev-&gt;p,同时初始化dev-&gt;p-&gt;klist_children</div><div>
</div><div>
</div><div>void device_initialize(struct device *dev)

{

     <font color="#FF0000">dev-&gt;kobj.kset = devices_kset;</font>

     <font color="#FF0000">kobject_init(&amp;dev-&gt;kobj, &amp;device_ktype);</font>

     INIT_LIST_HEAD(&amp;dev-&gt;dma_pools);

     mutex_init(&amp;dev-&gt;mutex);

     lockdep_set_novalidate_class(&amp;dev-&gt;mutex);

     spin_lock_init(&amp;dev-&gt;devres_lock);

     INIT_LIST_HEAD(&amp;dev-&gt;devres_head);

     device_pm_init(dev);

     set_dev_node(dev, -1);

}</div><div>
</div><div>
</div><div>int device_add(struct device *dev)

{

     struct device *parent = NULL;

     struct class_interface *class_intf;

     int error = -EINVAL;

     <font color="#FF0000">dev = get_device(dev);</font>

     if (!dev)

          goto done;

     if (<font color="#FF0000">!dev-&gt;p</font>) {

          <font color="#FF0000">error = device_private_init(dev);</font>

          if (error)

               goto done;

     }

     /*

     * for statically allocated devices, which should all be converted

     * some day, we need to initialize the name. We prevent reading back

     * the name, and force the use of dev_name()

     */

     if (<font color="#FF0000">dev-&gt;init_name</font>) {

          <font color="#FF0000">dev_set_name(dev, &quot;%s&quot;, dev-&gt;init_name);</font>

          <font color="#FF0000">dev-&gt;init_name = NULL;</font>

     }

     if (!dev_name(dev)) {

          error = -EINVAL;

          goto name_error;

     }

     pr_debug(&quot;device: '%s': %s\n&quot;, dev_name(dev), __func__);

     <font color="#FF0000">parent = get_device(dev-&gt;parent);</font>

     <font color="#FF0000">setup_parent(dev, parent);</font>

     /* use parent numa_node */

     if (parent)

          set_dev_node(dev, dev_to_node(parent));

     /* first, register with generic layer. */

     /* we require the name to be set before, and pass NULL */

     <font color="#FF0000">error = kobject_add(&amp;dev-&gt;kobj, dev-&gt;kobj.parent, NULL);</font>

     if (error)

          goto Error;

     /* notify platform of device entry */

     if (platform_notify)

          platform_notify(dev);

     <font color="#FF0000">error = device_create_file(dev, &amp;uevent_attr);</font>

     if (error)

          goto attrError;

     if (<font color="#FF0000">MAJOR(dev-&gt;devt)</font>) {

          <font color="#FF0000">error = device_create_file(dev, &amp;devt_attr);</font>

          if (error)

               goto ueventattrError;

          <font color="#FF0000">error = device_create_sys_dev_entry(dev);</font>

          if (error)

               goto devtattrError;

          <font color="#FF0000">devtmpfs_create_node(dev);</font>

     }

     <font color="#FF0000">error = device_add_class_symlinks(dev);</font>

     if (error)

          goto SymlinkError;

     <font color="#FF0000">error = device_add_attrs(dev);</font>

     if (error)

          goto AttrsError;

     <font color="#FF0000">error = bus_add_device(dev);</font>

     if (error)

          goto BusError;

     <font color="#FF0000">error = dpm_sysfs_add(dev);</font>

     if (error)

          goto DPMError;

     <font color="#FF0000">device_pm_add(dev);</font>

     /* Notify clients of device addition.  This call must come

     * after dpm_sysf_add() and before kobject_uevent().

     */

     if (dev-&gt;bus)

          blocking_notifier_call_chain(&amp;dev-&gt;bus-&gt;p-&gt;bus_notifier,

                              BUS_NOTIFY_ADD_DEVICE, dev);

     <font color="#FF0000">kobject_uevent(&amp;dev-&gt;kobj, KOBJ_ADD);</font>

     <font color="#FF0000">bus_probe_device(dev);</font>

     if (<font color="#FF0000">parent</font>)

          <font color="#FF0000">klist_add_tail(&amp;dev-&gt;p-&gt;knode_parent,

                      &amp;parent-&gt;p-&gt;klist_children);</font>

     if (<font color="#FF0000">dev-&gt;class</font>) {

          mutex_lock(&amp;dev-&gt;class-&gt;p-&gt;class_mutex);

          /* tie the class to the device */

          <font color="#FF0000">klist_add_tail(&amp;dev-&gt;knode_class,

                      &amp;dev-&gt;class-&gt;p-&gt;klist_devices);</font>

          /* notify any interfaces that the device is here */

          list_for_each_entry(class_intf,

                        &amp;dev-&gt;class-&gt;p-&gt;class_interfaces, node)

               if (class_intf-&gt;add_dev)

                    class_intf-&gt;add_dev(dev, class_intf);

          mutex_unlock(&amp;dev-&gt;class-&gt;p-&gt;class_mutex);

     }

done:

     <font color="#FF0000">put_device(dev);</font>

     return error;

DPMError:

     bus_remove_device(dev);

BusError:

     device_remove_attrs(dev);

AttrsError:

     device_remove_class_symlinks(dev);

SymlinkError:

     if (MAJOR(dev-&gt;devt))

          devtmpfs_delete_node(dev);

     if (MAJOR(dev-&gt;devt))

          device_remove_sys_dev_entry(dev);

devtattrError:

     if (MAJOR(dev-&gt;devt))

          device_remove_file(dev, &amp;devt_attr);

ueventattrError:

     device_remove_file(dev, &amp;uevent_attr);

attrError:

     kobject_uevent(&amp;dev-&gt;kobj, KOBJ_REMOVE);

     kobject_del(&amp;dev-&gt;kobj);

Error:

     cleanup_device_parent(dev);

     if (parent)

          put_device(parent);

name_error:

     kfree(dev-&gt;p);

     dev-&gt;p = NULL;

     goto done;

}</div><div>void device_del(struct device *dev);</div><div>// 目前没有看懂devtmpfs_create_node,百度解释:...</div><div>// 添加/删除 device </div><div>
</div><div>
</div><div>int device_register(struct device *dev)

{

     <font color="#FF0000">device_initialize</font>(dev);

     return <font color="#FF0000">device_add</font>(dev);

}</div><div>void device_unregister(struct device *dev);</div><div>// 注册/注销 设备</div><div>// 以上device_add,deivce_register函数必须确定位置与属性,包括以下:</div><div>// dev-&gt;parent,dev-&gt;bus,dev-&gt;class</div><div>// dev-&gt;init_name,dev-&gt;devt,dev-&gt;groups,dev-&gt;release</div><div>
</div><div>
</div><div>int __init devices_init(void)

{

     <font color="#FF0000">devices_kset = kset_create_and_add(&quot;devices&quot;, &amp;device_uevent_ops, NULL);</font>

     if (!devices_kset)

          return -ENOMEM;

     <font color="#FF0000">dev_kobj = kobject_create_and_add(&quot;dev&quot;, NULL);</font>

     if (!dev_kobj)

          goto dev_kobj_err;

     <font color="#FF0000">sysfs_dev_block_kobj = kobject_create_and_add(&quot;block&quot;, dev_kobj);</font>

     if (!sysfs_dev_block_kobj)

          goto block_kobj_err;

     <font color="#FF0000">sysfs_dev_char_kobj = kobject_create_and_add(&quot;char&quot;, dev_kobj);</font>

     if (!sysfs_dev_char_kobj)

          goto char_kobj_err;

     return 0;

char_kobj_err:

     kobject_put(sysfs_dev_block_kobj);

block_kobj_err:

     kobject_put(dev_kobj);

dev_kobj_err:

     kset_unregister(devices_kset);

     return -ENOMEM;

}</div><div>// 创建/sys/dev目录的kobject

// 创建/sys/dev/char目录的kobject

// 创建/sys/dev/block目录的kobject

// 创建/sys/devices目录的kset</div><div>
</div><div>
</div><div>static struct device *next_device(struct klist_iter *i);</div><div>int <font color="#FF0000">device_for_each_child</font>(struct device *parent, void *data,

                 int (*fn)(struct device *dev, void *data));</div><div>// 对dev下的每个子device调用指定函数一次,子device即dev-&gt;p-&gt;klist_children</div><div>struct device *<font color="#FF0000">device_find_child</font>(struct device *parent, void *data,

                    int (*match)(struct device *dev, void *data));</div><div>// 对dev下的每个子device调用匹配函数找到匹配device,子device即dev-&gt;p-&gt;klist_children</div><div>
</div><div>
</div><div>inline struct root_device *to_root_device(struct device *d);
</div><div>static void root_device_release(struct device *dev);
</div><div>struct device *__root_device_register(const char *name, struct module *owner);
</div><div>void root_device_unregister(struct device *dev);
</div><div>// 注册/注销 root_device,很少用,忽略这里.</div><div>
</div><div>
</div><div>
</div><div>static void device_create_release(struct device *dev);
</div><div>struct device *<font color="#FF0000">device_create_vargs</font>(struct class *class, struct device *parent,

                       dev_t devt, void *drvdata, const char *fmt,

                       va_list args);</div><div>struct device *<font color="#FF0000">device_create</font>(struct class *class, struct device *parent,

                    dev_t devt, void *drvdata, const char *fmt, ...);</div><div>static int __match_devt(struct device *dev, void *data);</div><div>void <font color="#FF0000">device_destroy</font>(struct class *class, dev_t devt);</div><div>int <font color="#FF0000">device_rename</font>(struct device *dev, const char *new_name);</div><div>static int device_move_class_links(struct device *dev,

                       struct device *old_parent,

                       struct device *new_parent);</div><div>int <font color="#FF0000">device_move</font>(struct device *dev, struct device *new_parent,

          enum dpm_order dpm_order);</div><div>// 申请分配/释放销毁/改名移动 struct device相关函数,应该这里是最常用的API</div><div>void device_shutdown(void);</div><div>// 系统关闭调用</div><div>
</div></span>
</div>

* * *

<a name="2281"/>

# linux/device.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/6/30 19:27_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/6/30 19:40_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
struct device_type {

     const char *name;

     const struct attribute_group **groups;

     int (*uevent)(struct device *dev, struct kobj_uevent_env *env);

     char *(*devnode)(struct device *dev, mode_t *mode);

     void (*release)(struct device *dev);

     const struct dev_pm_ops *pm;

};
<div>
</div><div>struct device_attribute {

     struct attribute     attr;

     ssize_t (*show)(struct device *dev, struct device_attribute *attr,

               char *buf);

     ssize_t (*store)(struct device *dev, struct device_attribute *attr,

               const char *buf, size_t count);

};</div><div>
</div><div>#define DEVICE_ATTR(_name, _mode, _show, _store) \

struct device_attribute dev_attr_##_name = __ATTR(_name, _mode, _show, _store)</div><div>
</div><div>
</div><div>
</div><div>
</div><div>struct device {

     struct device          *parent;

     struct device_private     *p;

     struct kobject kobj;

     const char          *init_name; /* initial name of the device */

     const struct device_type *type;

     struct mutex          mutex;     /* mutex to synchronize calls to

                         * its driver.

                         */

     struct bus_type     *bus;          /* type of bus device is on */

     struct device_driver *driver;     /* which driver has allocated this

                            device */

     void          *platform_data;     /* Platform specific data, device

                            core doesn't touch it */

     struct dev_pm_info     power;

     struct dev_power_domain     *pwr_domain;

#ifdef CONFIG_NUMA

     int          numa_node;     /* NUMA node this device is close to */

#endif

     u64          *dma_mask;     /* dma mask (if dma'able device) */

     u64          coherent_dma_mask;/* Like dma_mask, but for

                              alloc_coherent mappings as

                              not all hardware supports

                              64 bit addresses for consistent

                              allocations such descriptors. */

     struct device_dma_parameters *dma_parms;

     struct list_head     dma_pools;     /* dma pools (if dma'ble) */

     struct dma_coherent_mem     *dma_mem; /* internal for coherent mem

                              override */

     /* arch specific additions */

     struct dev_archdata     archdata;

     struct device_node     *of_node; /* associated device tree node */

     dev_t               devt;     /* dev_t, creates the sysfs &quot;dev&quot; */

     spinlock_t          devres_lock;

     struct list_head     devres_head;

     struct klist_node     knode_class;

     struct class          *class;

     const struct attribute_group **groups;     /* optional groups */

     void     (*release)(struct device *dev);

};</div><div>
</div><div>EXPORT_SYMBOL_GPL(device_schedule_callback_owner);

#define device_schedule_callback(dev, func)               \

     device_schedule_callback_owner(dev, func, THIS_MODULE)</div><div>
</div><div>typedef void (*dr_release_t)(struct device *dev, void *res);

typedef int (*dr_match_t)(struct device *dev, void *res, void *match_data);</div><div>
</div><div>static inline const char *dev_name(const struct device *dev);
</div><div>EXPORT_SYMBOL_GPL(dev_set_name);</div><div>EXPORT_SYMBOL(dev_driver_string);</div><div>static inline unsigned int dev_get_uevent_suppress(const struct device *dev);</div><div>static inline void dev_set_uevent_suppress(struct device *dev, int val);</div><div>static inline int device_is_registered(struct device *dev);</div><div>static inline void device_enable_async_suspend(struct device *dev);</div><div>static inline void device_disable_async_suspend(struct device *dev);</div><div>static inline bool device_async_suspend_enabled(struct device *dev);
</div><div>static inline void device_lock(struct device *dev);</div><div>static inline int device_trylock(struct device *dev);
</div><div>static inline void device_unlock(struct device *dev);
</div><div>EXPORT_SYMBOL_GPL(device_initialize);

EXPORT_SYMBOL_GPL(device_add);

EXPORT_SYMBOL_GPL(device_register);

EXPORT_SYMBOL_GPL(device_del);

EXPORT_SYMBOL_GPL(device_unregister);</div><div>EXPORT_SYMBOL_GPL(device_for_each_child);

EXPORT_SYMBOL_GPL(device_find_child);</div><div>EXPORT_SYMBOL_GPL(device_create_vargs);

EXPORT_SYMBOL_GPL(device_create);

EXPORT_SYMBOL_GPL(device_destroy);

EXPORT_SYMBOL_GPL(device_rename);

EXPORT_SYMBOL_GPL(device_move);</div><div>EXPORT_SYMBOL_GPL(get_device);

EXPORT_SYMBOL_GPL(put_device);</div><div>EXPORT_SYMBOL_GPL(device_create_file);

EXPORT_SYMBOL_GPL(device_remove_file);

EXPORT_SYMBOL_GPL(device_create_bin_file);

EXPORT_SYMBOL_GPL(device_remove_bin_file);</div><div>EXPORT_SYMBOL_GPL(__root_device_register);

EXPORT_SYMBOL_GPL(root_device_unregister);</div><div>
</div></span>
</div>

* * *

<a name="2313"/>

# /drivers/base/bus.c

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/7/3 14:59_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/17 17:19_</td></tr>
<tr><td>**来源：**</td><td><a href="http://blog.csdn.net/qb_2008/article/details/6851394">_http://blog.csdn.net/qb_2008/article/details/6851394_</a></td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>// **<font color="#FF0000">总结:struct bus_type *bus的私有数据为struct subsys_private</font>**</div><div>// **bus的实例不是kobj而是kset(即bus-&gt;p-&gt;subsys)!!!**</div><div>// **driver的实例是kobj,加入某种bus,即bus-&gt;p-&gt;subsys管理driver-&gt;p-&gt;kobj**</div><div>
</div><div>static struct bus_type *<font color="#FF0000">bus_get</font>(struct bus_type *bus);</div><div>static void <font color="#FF0000">bus_put</font>(struct bus_type *bus);</div><div>// 增加/减少 bus-&gt;p-&gt;subsys 引用计数</div><div>
</div><div>
</div><div>static void klist_devices_get(struct klist_node *n);</div><div>static void klist_devices_put(struct klist_node *n);</div><div>// 涉及struct bus_type *bus的私有成员struct subsys_private *priv;的成员struct klist klist_devices;</div><div>// 调用源码klist_init(&amp;priv-&gt;klist_devices, klist_devices_get, klist_devices_put);</div><div>
</div><div>
</div><div>struct kset *bus_get_kset(struct bus_type *bus);</div><div>struct klist *bus_get_device_klist(struct bus_type *bus);</div><div>// 返回所需指针,看函数名描述</div><div>static int __must_check bus_rescan_devices_helper(struct device *dev,

                                void *data);</div><div>int bus_rescan_devices(struct bus_type *bus);</div><div>int device_reprobe(struct device *dev);</div><div>
</div><div>
</div><div>#define to_drv_attr(_attr) container_of(_attr, struct driver_attribute, attr)</div><div>static ssize_t drv_attr_show(struct kobject *kobj, struct attribute *attr,

                    char *buf);</div><div>static ssize_t drv_attr_store(struct kobject *kobj, struct attribute *attr,

                     const char *buf, size_t count);</div><div>static const struct sysfs_ops driver_sysfs_ops = {

     .show     = drv_attr_show,

     .store     = drv_attr_store,

};</div><div>static void driver_release(struct kobject *kobj);</div><div>static struct kobj_type <font color="#FF0000">driver_ktype</font> = {

     .sysfs_ops     = &amp;driver_sysfs_ops,

     .release     = driver_release,

};</div><div>// 某个<font color="#FF0000">驱动</font>kobj(<font color="#FF0000">注意是驱动！！</font>)的kobj_type,访问这个驱动目录下属性文件时均会调drv_attr_show和drv_attr_store</div><div>// 位于bus_add_driver函数调用kobject_init_and_add(&amp;priv-&gt;kobj, &amp;driver_ktype, NULL, &quot;%s&quot;, drv-&gt;name);</div><div>
</div><div>
</div><div>#define to_bus_attr(_attr) container_of(_attr, struct bus_attribute, attr)
</div><div>static ssize_t bus_attr_show(struct kobject *kobj, struct attribute *attr,

                    char *buf);</div><div>static ssize_t bus_attr_store(struct kobject *kobj, struct attribute *attr,

                     const char *buf, size_t count);</div><div>static const struct sysfs_ops bus_sysfs_ops = {

     .show     = bus_attr_show,

     .store     = bus_attr_store,

};</div><div>static struct kobj_type <font color="#FF0000">bus_ktype</font> = {

     .sysfs_ops     = &amp;bus_sysfs_ops,

};</div><div>// 某种<font color="#FF0000">总线</font>bus-&gt;p-&gt;subsys-&gt;kobj(<font color="#FF0000">注意bus的不是kobj是kset！！</font>)的kobj_type,访问这个...时均会调bus_attr_show和bus_attr_store</div><div>// 位于bus_register调用,默认赋值这个bus-&gt;p-&gt;subsys.kobj.ktype</div><div>
</div><div>
</div><div>static int bus_uevent_filter(struct kset *kset, struct kobject *kobj);
</div><div>static const struct kset_uevent_ops bus_uevent_ops = {

     .filter = bus_uevent_filter,

};</div><div>static struct kset *<font color="#FF0000">bus_kset</font>;</div><div>int __init <font color="#FF0000">buses_init</font>(void);</div><div>// 通过buses_init创建/sys/bus目录,由bus_kset管理(它是一个kobj集合,即kset),还有他的uevent</div><div>// 调用源码:bus_kset = kset_create_and_add(&quot;bus&quot;, &amp;bus_uevent_ops, NULL);</div><div>
</div><div>
</div><div>int <font color="#FF0000">bus_create_file</font>(struct bus_type *bus, struct bus_attribute *attr);</div><div>void <font color="#FF0000">bus_remove_file</font>(struct bus_type *bus, struct bus_attribute *attr);</div><div>// 添加/删除 目标 bus-&gt;p-&gt;subsys 属性文件</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>static struct device *next_device(struct klist_iter *i);</div><div>int <font color="#FF0000">bus_for_each_dev</font>(struct bus_type *bus, struct device *start,

               void *data, int (*fn)(struct device *, void *));</div><div>struct device *<font color="#FF0000">bus_find_device</font>(struct bus_type *bus,

                      struct device *start, void *data,

                      int (*match)(struct device *dev, void *data));</div><div>static int match_name(struct device *dev, void *data);</div><div>struct device *<font color="#FF0000">bus_find_device_by_name</font>(struct bus_type *bus,

                           struct device *start, const char *name)

{

     return bus_find_device(bus, start, (void *)name, match_name);

}</div><div>// bus_for_each_dev是以bus的设备链表中每个设备为参数,调用指定的处理函数</div><div>// bus_find_device是寻找bus设备链表中的某个设备,使用指定的匹配函数</div><div>// bus_find_device_by_name使用match_name配合bus_find_device寻找匹配设备</div><div>// <font color="#FF0000">bus-&gt;p-&gt;klist_devices是struct klist结构</font><font color="#FF0000">,即归属这个bus的device的链表节点表头</font></div><div>static struct device_driver *next_driver(struct klist_iter *i);</div><div>int <font color="#FF0000">bus_for_each_drv</font>(struct bus_type *bus, struct device_driver *start,

               void *data, int (*fn)(struct device_driver *, void *));</div><div>// bus_for_each_drv对bus的驱动链表中的每个驱动调用指定的函数</div><div>
</div><div>
</div><div>static int device_add_attrs(struct bus_type *bus, struct device *dev);</div><div>static void device_remove_attrs(struct bus_type *bus, struct device *dev);</div><div>// 往struct device *dev的kobj中添加bus-&gt;dev_attrs属性文件</div><div>int <font color="#FF0000">bus_add_device</font>(struct device *dev)</div><div>{

     <font color="#FF0000">struct bus_type *bus = bus_get(dev-&gt;bus);</font>

     int error = 0;

     if (<font color="#FF0000">bus</font>) {

          pr_debug(&quot;bus: '%s': add device %s\n&quot;, bus-&gt;name, dev_name(dev));

          <font color="#FF0000">error = device_add_attrs(bus, dev);</font>

          if (error)

               goto out_put;

          <font color="#FF0000">error = sysfs_create_link(&amp;bus-&gt;p-&gt;devices_kset-&gt;kobj,

                              &amp;dev-&gt;kobj, dev_name(dev));</font>

          if (error)

               goto out_id;

          <font color="#FF0000">error = sysfs_create_link(&amp;dev-&gt;kobj,

                    &amp;dev-&gt;bus-&gt;p-&gt;subsys.kobj, &quot;subsystem&quot;);</font>

          if (error)

               goto out_subsys;

          <font color="#FF0000">klist_add_tail(&amp;dev-&gt;p-&gt;knode_bus, &amp;bus-&gt;p-&gt;klist_devices);</font>

     }

     return 0;

out_subsys:

     sysfs_remove_link(&amp;bus-&gt;p-&gt;devices_kset-&gt;kobj, dev_name(dev));

out_id:

     device_remove_attrs(bus, dev);

out_put:

     bus_put(dev-&gt;bus);

     return error;

}</div><div>void <font color="#FF0000">bus_remove_device</font>(struct device *dev);</div><div>// 添加/删除 这个dev所属bus的属性到这个dev的kobj中,即添加/删除dev-&gt;bus-&gt;dev_attrs到dev-&gt;kobj中</div><div>// 添加/删除 这个dev所属bus-&gt;p-&gt;devices_kset-&gt;kobj指向这个dev-&gt;kobj的软链接,名字为设备名</div><div>// 例如:/sys/bus/i2c/devices/<font color="#5DDAFF">i2c-0</font> ---&gt; /sys/devices/platform/s3c2440-i2c.0/i2c-0</div><div>// 添加/删除 这个dev指向其所属bus软链接,文件名subsystem</div><div>// 例如:/sys/devices/platform/s3c2440-i2c.0/<font color="#5DDAFF">subsystem</font> ---&gt; /sys/bus/i2c</div><div>
</div><div>
</div><div>void <font color="#FF0000">bus_probe_device</font>(struct device *dev)

{

     struct bus_type *bus = dev-&gt;bus;

     int ret;

     if (bus &amp;&amp; bus-&gt;p-&gt;drivers_autoprobe) {

          ret = device_attach(dev);

          WARN_ON(ret &lt; 0);

     }

}</div><div>// 根据device所属bus判断是否运行自动探测driver,一般情况bus-&gt;p-&gt;drivers_autoprobe均置一,即:允许</div><div>// 位于device_add调用,说明添加设备之后会自动探测驱动</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>static int <font color="#FF0000">driver_add_attrs</font>(struct bus_type *bus, struct device_driver *drv);</div><div>static void <font color="#FF0000">driver_remove_attrs</font>(struct bus_type *bus,

                    struct device_driver *drv);</div><div>// 添加/删除 至drv-&gt;p-&gt;kobj中bus-&gt;drv_attrs的属性文件</div><div>// 位于bus_add_driver调用,源码:driver_add_attrs(bus, drv);</div><div>
</div><div>
</div><div>#ifdef CONFIG_HOTPLUG</div><div>// 以下这些应该支持热插拔设备才拥有的属性,即插拔自动装载/卸载设备</div><div>static ssize_t driver_unbind(struct device_driver *drv,

                    const char *buf, size_t count);</div><div>static DRIVER_ATTR(<font color="#FF0000">unbind</font>, S_IWUSR, NULL, driver_unbind);</div><div>static ssize_t driver_bind(struct device_driver *drv,

                  const char *buf, size_t count);</div><div>static DRIVER_ATTR(<font color="#FF0000">bind</font>, S_IWUSR, NULL, driver_bind);</div><div>static int __must_check <font color="#FF0000">add_bind_files</font>(struct device_driver *drv);</div><div>static void <font color="#FF0000">remove_bind_files</font>(struct device_driver *drv);</div><div>// 类型struct driver_attribute的驱动属性文件,文件名bind和unbind</div><div>// 以上属性文件由add_bind_files和remove_bind_files调用添加/删除</div><div>// 而这两个函数由bus_add_driver和bus_remove_driver调用 添加/删除</div><div>// <font color="#FF0000">bind和unbind位于sys/bus/总线类型/drivers/xxx/下各有一个</font>,例如:sys/bus/i2c/drivers/s5p_ddc/<font color="#FF0000">bind</font></div><div>
</div><div>static ssize_t show_drivers_autoprobe(struct bus_type *bus, char *buf);</div><div>static ssize_t store_drivers_autoprobe(struct bus_type *bus,

                           const char *buf, size_t count);</div><div>static BUS_ATTR(<font color="#FF0000">drivers_autoprobe</font>, S_IWUSR | S_IRUGO,

          show_drivers_autoprobe, store_drivers_autoprobe);</div><div>static ssize_t store_drivers_probe(struct bus_type *bus,

                       const char *buf, size_t count);</div><div>static BUS_ATTR(<font color="#FF0000">drivers_probe</font>, S_IWUSR, NULL, store_drivers_probe);</div><div>static int <font color="#FF0000">add_probe_files</font>(struct bus_type *bus);</div><div>static void <font color="#FF0000">remove_probe_files</font>(struct bus_type *bus);</div><div>// 类型struct bus_attribute的总线属性文件,文件名drivers_probe和drivers_autoprobe</div><div>// 以上属性文件由add_probe_files和remove_probe_files调用添加/删除</div><div>// 而这两个函数由bus_register和bus_unregister调用 添加/删除</div><div>// <font color="#FF0000">drivers_probe和drivers_autoprobe位于sys/bus/总线类型/各有一个</font>,例如:sys/bus/i2c/<font color="#FF0000">drivers_probe</font></div><div>#endif</div><div>
</div><div>
</div><div>static ssize_t driver_uevent_store(struct device_driver *drv,

                       const char *buf, size_t count);

static DRIVER_ATTR(<font color="#FF0000">uevent</font>, S_IWUSR, NULL, driver_uevent_store);

// 类型struct driver_attribute的驱动属性文件,<font color="#FF0000">文件名uevent</font>

// 位于bus_add_driver调用<font color="#FF0000">往struct device_driver *drv中添加uevent属性文件</font>

// 例如:/sys/bus/i2c/drivers/s5p_ddc/uevent</div><div>
</div><div>
</div><div>int bus_add_driver(struct device_driver *drv)

{

     struct bus_type *bus;

     struct driver_private *priv;

     int error = 0;

     <font color="#FF0000">bus = bus_get(drv-&gt;bus);</font>

     if (!bus)

          return -EINVAL;

     pr_debug(&quot;bus: '%s': add driver %s\n&quot;, bus-&gt;name, drv-&gt;name);

     <font color="#FF0000">priv = kzalloc(sizeof(*priv), GFP_KERNEL);</font>

     if (!priv) {

          error = -ENOMEM;

          goto out_put_bus;

     }

     <font color="#FF0000">klist_init(&amp;priv-&gt;klist_devices, NULL, NULL);</font>

     <font color="#FF0000">priv-&gt;driver = drv;</font>

     <font color="#FF0000">drv-&gt;p = priv;</font>

     <font color="#FF0000">priv-&gt;kobj.kset = bus-&gt;p-&gt;drivers_kset;</font>

     <font color="#FF0000">error = kobject_init_and_add(&amp;priv-&gt;kobj, &amp;driver_ktype, NULL,

                         &quot;%s&quot;, drv-&gt;name);</font>

     if (error)

          goto out_unregister;

     if (<font color="#FF0000">drv-&gt;bus-&gt;p-&gt;drivers_autoprobe</font>) {

          <font color="#FF0000">error = driver_attach(drv);</font>

          if (error)

               goto out_unregister;

     }

     <font color="#FF0000">klist_add_tail(&amp;priv-&gt;knode_bus, &amp;bus-&gt;p-&gt;klist_drivers);</font>

     <font color="#FF0000">module_add_driver(drv-&gt;owner, drv);</font>

     <font color="#FF0000">error = driver_create_file(drv, &amp;driver_attr_uevent);</font>

     if (error) {

          printk(KERN_ERR &quot;%s: uevent attr (%s) failed\n&quot;,

               __func__, drv-&gt;name);

     }

     <font color="#FF0000">error = driver_add_attrs(bus, drv);</font>

     if (error) {

          /* How the hell do we get out of this pickle? Give up */

          printk(KERN_ERR &quot;%s: driver_add_attrs(%s) failed\n&quot;,

               __func__, drv-&gt;name);

     }

     if (<font color="#FF0000">!drv-&gt;suppress_bind_attrs</font>) {

          <font color="#FF0000">error = add_bind_files(drv);</font>

          if (error) {

               /* Ditto */

               printk(KERN_ERR &quot;%s: add_bind_files(%s) failed\n&quot;,

                    __func__, drv-&gt;name);

          }

     }

     <font color="#FF0000">kobject_uevent(&amp;priv-&gt;kobj, KOBJ_ADD);</font>

     return 0;

out_unregister:

     kobject_put(&amp;priv-&gt;kobj);

     kfree(drv-&gt;p);

     drv-&gt;p = NULL;

out_put_bus:

     bus_put(bus);

     return error;

}</div><div>void bus_remove_driver(struct device_driver *drv);</div><div>// 原来添加driver的目录在这里,居然在bus.c文件里面实现</div><div>// 注册/注销 这个driver至bus-&gt;p-&gt;subsys,这个driver必须确定自己所属哪种bus</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>
</div><div>static int <font color="#FF0000">bus_add_attrs</font>(struct bus_type *bus);</div><div>static void <font color="#FF0000">bus_remove_attrs</font>(struct bus_type *bus);</div><div>// 添加/删除 这个bus自己的属性文件到自己目录,即:把bus-&gt;bus_attrs至bus-&gt;p-&gt;subsys.kobj</div><div>
</div><div>
</div><div>static void klist_devices_get(struct klist_node *n);</div><div>static void klist_devices_put(struct klist_node *n);</div><div>// 添加/删除 节点所属deivce的引用计数</div><div>// 位于bus_register调用,用于管理bus-&gt;p-&gt;klist_devices链表上设备get/put函数</div><div>
</div><div>
</div><div>static ssize_t bus_uevent_store(struct bus_type *bus,

                    const char *buf, size_t count);

static BUS_ATTR(<font color="#FF0000">uevent</font>, S_IWUSR, NULL, bus_uevent_store);

// 某种总线的属性文件,例如:/sys/bus/i2c/<font color="#FF0000">uevent</font>

// 位于bus_register函数调用,<font color="#FF0000">所以是给某种bus添加uevent的属性</font></div><div>// 源码:retval = bus_create_file(bus, &amp;bus_attr_uevent);</div><div>
</div><div>
</div><div>int <font color="#FF0000">bus_register</font>(struct bus_type *bus)</div><div>{

     int retval;

     struct subsys_private *priv;

     <font color="#FF0000">priv = kzalloc(sizeof(struct subsys_private), GFP_KERNEL);</font>

     if (!priv)

          return -ENOMEM;

     <font color="#FF0000">priv-&gt;bus = bus;</font>

     <font color="#FF0000">bus-&gt;p = priv;</font>

     BLOCKING_INIT_NOTIFIER_HEAD(&amp;priv-&gt;bus_notifier);

     <font color="#FF0000">retval = kobject_set_name(&amp;priv-&gt;subsys.kobj, &quot;%s&quot;, bus-&gt;name);</font>

     if (retval)

          goto out;

     <font color="#FF0000">priv-&gt;subsys.kobj.kset = bus_kset;</font>

     <font color="#FF0000">priv-&gt;subsys.kobj.ktype = &amp;bus_ktype;</font>

     <font color="#FF0000">priv-&gt;drivers_autoprobe = 1;</font>

     <font color="#FF0000">retval = kset_register(&amp;priv-&gt;subsys);</font>

     if (retval)

          goto out;

     <font color="#FF0000">retval = bus_create_file(bus, &amp;bus_attr_uevent);</font>

     if (retval)

          goto bus_uevent_fail;

     <font color="#FF0000">priv-&gt;devices_kset = kset_create_and_add(&quot;devices&quot;, NULL,

                              &amp;priv-&gt;subsys.kobj);</font>

     if (!priv-&gt;devices_kset) {

          retval = -ENOMEM;

          goto bus_devices_fail;

     }

     <font color="#FF0000">priv-&gt;drivers_kset = kset_create_and_add(&quot;drivers&quot;, NULL,

                              &amp;priv-&gt;subsys.kobj);</font>

     if (!priv-&gt;drivers_kset) {

          retval = -ENOMEM;

          goto bus_drivers_fail;

     }

     <font color="#FF0000">klist_init(&amp;priv-&gt;klist_devices, klist_devices_get, klist_devices_put);</font>

     <font color="#FF0000">klist_init(&amp;priv-&gt;klist_drivers, NULL, NULL);</font>

     <font color="#FF0000">retval = add_probe_files(bus);</font>

     if (retval)

          goto bus_probe_files_fail;

     <font color="#FF0000">retval = bus_add_attrs(bus);</font>

     if (retval)

          goto bus_attrs_fail;

     pr_debug(&quot;bus: '%s': registered\n&quot;, bus-&gt;name);

     return 0;

bus_attrs_fail:

     remove_probe_files(bus);

bus_probe_files_fail:

     kset_unregister(bus-&gt;p-&gt;drivers_kset);

bus_drivers_fail:

     kset_unregister(bus-&gt;p-&gt;devices_kset);

bus_devices_fail:

     bus_remove_file(bus, &amp;bus_attr_uevent);

bus_uevent_fail:

     kset_unregister(&amp;bus-&gt;p-&gt;subsys);

out:

     kfree(bus-&gt;p);

     bus-&gt;p = NULL;

     return retval;

}</div><div>void <font color="#FF0000">bus_unregister</font>(struct bus_type *bus);</div><div>// 注册/注销 bus到系统当中</div><div>// 在这个bus-&gt;p-&gt;subsys.kobj创建devices和driver目录、bus-&gt;bus_attrs和uevent属性文件</div><div>// drivers_autoprobe和drivers_probe文件只有支持热插拔情况下才存在</div><div>
</div><div>
</div><div>int bus_register_notifier(struct bus_type *bus, struct notifier_block *nb);</div><div>int bus_unregister_notifier(struct bus_type *bus, struct notifier_block *nb);
</div><div>static void device_insertion_sort_klist(struct device *a, struct list_head *list,

                         int (*compare)(const struct device *a,

                                   const struct device *b));</div><div>void bus_sort_breadthfirst(struct bus_type *bus,

                  int (*compare)(const struct device *a,

                           const struct device *b));</div><div>// 不懂,暂时不管</div></span>
</div>

* * *

<a name="2355"/>

# /drivers/base/base.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/7/10 10:47_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/31 11:23_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>
</div><div>struct <font color="#FF0000">subsys_private</font> {

     struct kset subsys;                                                  // bus在sysfs中体现是kset,而不是kobj

     struct kset *devices_kset;                                       // 在sysfs中体现是这个bus目录下devices目录

     struct kset *drivers_kset;                                        // 在sysfs中体现是这个bus目录下drivers目录

     struct klist klist_devices;                                          // 管理属于这个bus的devices的链表表头

     struct klist klist_drivers;                                           // 管理属于这个bus的drivers的链表表头

     struct blocking_notifier_head bus_notifier;

     unsigned int drivers_autoprobe:1;                           // 表示该bus是否支持驱动自动装载

     struct bus_type *bus;                                             // 指向这个bus

     struct list_head class_interfaces;

     struct kset glue_dirs;

     struct mutex class_mutex;

     struct class *class;

};</div><div>// bus的私有数据结构类型</div><div>#define <font color="#FF0000">to_subsys_private</font>(obj) container_of(obj, struct subsys_private, subsys.kobj)</div><div>
</div><div>
</div><div>struct <font color="#FF0000">driver_private</font> {

     struct kobject kobj;                                                // 这个driver的kobj

     struct klist klist_devices;                                         // 归属这个driver的device的链表表头

     struct klist_node knode_bus;                                   // driver所属bus的链表节点

     struct module_kobject *mkobj;

     struct device_driver *driver;                                   // 指向这个driver

};</div><div>// drivers的私有数据结构类型

#define <font color="#FF0000">to_driver</font>(obj) container_of(obj, struct driver_private, kobj)</div><div>
</div><div>
</div><div>struct <font color="#FF0000">device_private</font> {

     struct klist klist_children;                                        // 管理归属他的device的链表表头

     struct klist_node knode_parent;                              // 这个device所属父device的链表节点

     struct klist_node knode_driver;                               // 这个device所属driver的链表节点

     struct klist_node knode_bus;                                  // 这个device所属bus的链表节点

     void *driver_data;

     struct device *device;                                           // 指向这个device

};</div><div>// device的私有数据类型,他的kobj没有放在struct device_private而是struct device之中

#define <font color="#FF0000">to_device_private_parent</font>(obj)     \

     container_of(obj, struct device_private, knode_parent)

#define <font color="#FF0000">to_device_private_driver</font>(obj)     \

     container_of(obj, struct device_private, knode_driver)

#define <font color="#FF0000">to_device_private_bus</font>(obj)     \

     container_of(obj, struct device_private, knode_bus)</div><div>
</div><div>
</div><div>extern int device_private_init(struct device *dev);

/* initialisation functions */

extern int <font color="#FF0000">devices_init</font>(void);</div><div><div>// 创建/sys/dev目录的kobject

// 创建/sys/dev/char目录的kobject

// 创建/sys/dev/block目录的kobject

// 创建/sys/devices目录的kset</div>
extern int <font color="#FF0000">buses_init</font>(void);

extern int <font color="#FF0000">classes_init</font>(void);

extern int <font color="#FF0000">firmware_init</font>(void);</div><div>#ifdef CONFIG_SYS_HYPERVISOR

extern int <font color="#FF0000">hypervisor_init</font>(void);

#else

static inline int <font color="#FF0000">hypervisor_init</font>(void) { return 0; }

#endif</div><div>extern int <font color="#FF0000">platform_bus_init</font>(void);

extern int <font color="#FF0000">system_bus_init</font>(void);

extern int <font color="#FF0000">cpu_dev_init</font>(void);</div><div><div>// 大多数函数都是系统启动时候调用,创建/sys/xxx目录的kobject或者kset</div>

extern int bus_add_device(struct device *dev);

extern void bus_probe_device(struct device *dev);

extern void bus_remove_device(struct device *dev);

extern int bus_add_driver(struct device_driver *drv);

extern void bus_remove_driver(struct device_driver *drv);

extern void driver_detach(struct device_driver *drv);

extern int driver_probe_device(struct device_driver *drv, struct device *dev);

static inline int driver_match_device(struct device_driver *drv,

                          struct device *dev)

{

     return drv-&gt;bus-&gt;match ? drv-&gt;bus-&gt;match(dev, drv) : 1;

}

extern char *make_class_name(const char *name, struct kobject *kobj);

extern int devres_release_all(struct device *dev);

extern struct kset *devices_kset;

#if defined(CONFIG_MODULES) &amp;&amp; defined(CONFIG_SYSFS)

extern void module_add_driver(struct module *mod, struct device_driver *drv);

extern void module_remove_driver(struct device_driver *drv);

#else

static inline void module_add_driver(struct module *mod,

                         struct device_driver *drv) { }

static inline void module_remove_driver(struct device_driver *drv) { }

#endif

#ifdef CONFIG_DEVTMPFS

extern int devtmpfs_init(void);

#else

static inline int devtmpfs_init(void) { return 0; }

#endif
</div><div>
</div><div>
</div></span>
</div>

* * *

<a name="2367"/>

# /drivers/base/driver.c

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/7/10 11:19_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/17 16:37_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>// <font color="#FF0000">**总结:driver_register/driver_unregister通常被某一类驱动调用**</font></div><div>
</div><div>
</div><div>static struct device *next_device(struct klist_iter *i);
</div><div>int <font color="#FF0000">driver_for_each_device</font>(struct device_driver *drv, struct device *start,

                  void *data, int (*fn)(struct device *, void *));</div><div>// 对drv管理的device链表中每个设备调用一次函数</div><div>struct device *<font color="#FF0000">driver_find_device</font>(struct device_driver *drv,

                      struct device *start, void *data,

                      int (*match)(struct device *dev, void *data));</div><div>// 对drv管理的device链表中通过匹配函数找到匹配设备</div><div>
</div><div>
</div><div>int <font color="#FF0000">driver_create_file</font>(struct device_driver *drv,

                 const struct driver_attribute *attr);</div><div>void <font color="#FF0000">driver_remove_file</font>(struct device_driver *drv,

               const struct driver_attribute *attr);</div><div>static int <font color="#FF0000">driver_add_groups</font>(struct device_driver *drv,

                    const struct attribute_group **groups);</div><div>static void <font color="#FF0000">driver_remove_groups</font>(struct device_driver *drv,

                    const struct attribute_group **groups);</div><div>// 添加/删除 这个drv-&gt;p-&gt;kobj的属性/属性集合</div><div>
</div><div>
</div><div><strike>int driver_add_kobj(struct device_driver *drv, struct kobject *kobj,

              const char *fmt, ...);</strike></div><div><strike>// 目前没有使用???</strike></div><div>
</div><div>
</div><div>struct device_driver *<font color="#FF0000">get_driver</font>(struct device_driver *drv);</div><div>void <font color="#FF0000">put_driver</font>(struct device_driver *drv);</div><div>// 增加/减少 drv使用计数</div><div>
</div><div>
</div><div>int <font color="#FF0000">driver_register</font>(struct device_driver *drv);</div><div>void <font color="#FF0000">driver_unregister</font>(struct device_driver *drv);</div><div>// 注册/注销 这个driver,大部分实现却在bus_add_driver/bus_remove_driver这两个函数实现</div><div>
</div><div>
</div><div>struct device_driver *<font color="#FF0000">driver_find</font>(const char *name, struct bus_type *bus);</div><div>// 在bus-&gt;p-&gt;drivers_kset寻找同名kobj,通过kobj返回这个driver</div><div>
</div></span>
</div>

* * *

<a name="2389"/>

# /drivers/base/dd.c

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/7/16 11:01_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/17 17:22_</td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;"><div>
</div><div>static void driver_bound(struct device *dev);</div><div>// 将device加入driver的设备链表,源码:klist_add_tail(&amp;dev-&gt;p-&gt;knode_driver, &amp;dev-&gt;driver-&gt;p-&gt;klist_devices);</div><div>static int driver_sysfs_add(struct device *dev);</div><div>static void driver_sysfs_remove(struct device *dev);</div><div>// 创建/删除 dev-&gt;driver-&gt;p-&gt;kobj 指向 dev-&gt;kobj软链接,名字:<font color="#5DDAFF">kobject_name(&amp;dev-&gt;kobj)</font></div><div>// 例如:/sys/bus/platform/drivers/s5pv210-uart/<font color="#5DDAFF">s5pv210-uart.0</font> ---&gt; /sys/devices/platform/s5pv210-uart.0</div><div>// 创建/删除 dev-&gt;kobj 指向 dev-&gt;driver-&gt;p-&gt;kobj软链接,名字:<font color="#5DDAFF">driver</font></div><div>// 例如:/sys/devices/platform/s5pv210-uart.0/<font color="#5DDAFF">driver</font> ---&gt; /sys/bus/platform/drivers/s5pv210-uart</div><div>int <font color="#FF0000">device_bind_driver</font>(struct device *dev);</div><div>// <font color="#FF0000">当device-&gt;driver存在时</font>,调用函数,绑定device与driver,同时创建如上链接文件</div><div>static int really_probe(struct device *dev, struct device_driver *drv);</div><div>// 与device_bind_driver差不多,如果dev-&gt;bus-&gt;probe或drv-&gt;probe存在则调用</div><div>int driver_probe_device(struct device_driver *drv, struct device *dev);</div><div>static int <font color="#FF0000">__device_attach</font>(struct device_driver *drv, void *data);</div><div>// <font color="#FF0000">当device-&gt;driver没有赋值</font>,对device-&gt;bus每个驱动调用函数,使用device-&gt;bus链表每个driver-&gt;bus-&gt;match匹配</div><div>int <font color="#FF0000">device_attach</font>(struct device *dev);</div><div>// 绑定dev与driver,创建相关链接文件,通常调用顺序:device_add()-&gt;bus_probe_device()-&gt;device_attach()</div><div>
</div><div>int driver_probe_done(void);</div><div>void wait_for_device_probe(void);</div><div>
</div><div>static int <font color="#FF0000">__driver_attach</font>(struct device *dev, void *data);</div><div>// 依然使用drv-&gt;bus-&gt;match,之后调用driver_probe_device,<font color="#FF0000">类似__device_attach</font></div><div>int <font color="#FF0000">driver_attach</font>(struct device_driver *drv);</div><div>// 绑定drv与device,创建相关链接文件,通常调用顺序:driver_register()-&gt;bus_add_driver()-&gt;driver_attach()</div><div>
</div><div>
</div><div>static void __device_release_driver(struct device *dev);</div><div>void device_release_driver(struct device *dev);</div><div>void driver_detach(struct device_driver *drv);</div><div>// 解除device与driver之间绑定</div><div>
</div><div>
</div><div>void *<font color="#FF0000">dev_get_drvdata</font>(const struct device *dev);</div><div>int <font color="#FF0000">dev_set_drvdata</font>(struct device *dev, void *data);</div><div>// 设置/获取 device私有数据,即:dev-&gt;p-&gt;driver_data</div><div>
</div><div>
</div><div>
</div><div>
</div></span>
</div>

* * *

<a name="2411"/>

# linux/platform_device.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2016/7/31 11:31_</td></tr>
<tr><td>**更新时间：**</td><td>_2016/7/31 12:00_</td></tr>
<tr><td>**来源：**</td><td><a href="http://www.wowotech.net/device_model/platform_device.html">_http://www.wowotech.net/device_model/platform_device.html_</a></td></tr>
</table>
</div>

<div>
<span style="word-wrap: break-word; -webkit-nbsp-mode: space; -webkit-line-break: after-white-space;">
<font color="#FF0000">struct platform_device</font> {

     const char     * <font color="#FF0000">name</font>;

     int          <font color="#FF0000">id</font>;

     struct device     dev;

     u32          <font color="#FF0000">num_resources</font>;

     struct resource     * <font color="#FF0000">resource</font>;

     const struct platform_device_id     *id_entry;

     /* MFD cell pointer */

     struct mfd_cell *mfd_cell;

     /* arch specific additions */

     struct pdev_archdata     archdata;

};

#define <font color="#FF0000">platform_get_device_id</font>(pdev)     ((pdev)-&gt;id_entry)

#define <font color="#FF0000">to_platform_device</font>(x) container_of((x), struct platform_device, dev)

// Platform设备的注册/注销接口

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_register</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_unregister</font>);

// 可以获取platform_device变量中的resource信息,以及直接获取IRQ的number等等.

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_get_resource</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_get_irq</font>);

EXPORT_SYMBOL_GPL(platform_get_resource_byname);

EXPORT_SYMBOL_GPL(platform_get_irq_byname);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_add_devices</font>);

// 懒人调用函数,似乎使用不多.

EXPORT_SYMBOL_GPL(platform_device_register_resndata);

static inline struct platform_device *platform_device_register_simple(

          const char *name, int id,

          const struct resource *res, unsigned int num)

{

     return platform_device_register_resndata(NULL, name, id,

               res, num, NULL, 0);

}

static inline struct platform_device *platform_device_register_data(

          struct device *parent, const char *name, int id,

          const void *data, size_t size)

{

     return platform_device_register_resndata(parent, name, id,

               NULL, 0, data, size);

}
<div>

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_alloc</font>);           // 以name和id为参数,动态分配一个struct platform_device变量.

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_add_resources</font>);   // 向platform device中增加资源描述

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_add_data</font>);        // 向platform device中添加自定义的数据(保存在pdev-&gt;dev.platform_data指针中)

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_add</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_del</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_device_put</font>);

<font color="#FF0000">struct platform_driver</font> {

     int (*<font color="#FF0000">probe</font>)(struct platform_device *);

     int (*<font color="#FF0000">remove</font>)(struct platform_device *);

     void (*shutdown)(struct platform_device *);

     int (*suspend)(struct platform_device *, pm_message_t state);

     int (*resume)(struct platform_device *);

     struct device_driver driver;

     const struct platform_device_id *id_table;

};

// Platform驱动的注册/注销接口

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_driver_register</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_driver_unregister</font>);

EXPORT_SYMBOL_GPL(<font color="#FF0000">platform_driver_probe</font>); // 主动执行probe动作

// 设置或者获取driver保存在device变量中的私有数据

static inline void *<font color="#FF0000">platform_get_drvdata</font>(const struct platform_device *pdev)

{

     return dev_get_drvdata(&amp;pdev-&gt;dev);

}

static inline void <font color="#FF0000">platform_set_drvdata</font>(struct platform_device *pdev, void *data)

{

     dev_set_drvdata(&amp;pdev-&gt;dev, data);

}

// 懒人API,似乎使用不多(测试时候貌似可以使用).

EXPORT_SYMBOL_GPL(platform_create_bundle);

</div></span>
</div>

* * *

<a name="3121"/>

# /include/linux/pinctrl/consumer.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/16 16:21_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/16 16:40_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">/*</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* Consumer interface the pin control subsystem</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">*</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* Copyright (C) 2012 ST-Ericsson SA</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* Written on behalf of Linaro for ST-Ericsson</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* Based on bits of regulator core, gpio core and clk core</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">*</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* Author: Linus Walleij &lt;</span><a href="mailto:linus.walleij@linaro.org" style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">linus.walleij@linaro.org</a><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">&gt;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">*</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">* License terms: GNU General Public License (GPL) version 2</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">*/</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#ifndef __LINUX_PINCTRL_CONSUMER_H</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#define __LINUX_PINCTRL_CONSUMER_H</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#include &lt;linux/err.h&gt;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#include &lt;linux/list.h&gt;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#include &lt;linux/seq_file.h&gt;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#include &lt;linux/pinctrl/pinctrl-state.h&gt;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">/* This struct is private to the core and should be regarded as a cookie */</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">struct pinctrl;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">struct pinctrl_state;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">struct device;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#ifdef CONFIG_PINCTRL</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">/* External interface to pin control */</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_request_gpio(unsigned gpio);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    <span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // GPIO申请函数,仅供GPIO子系统调用???</span></span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern void pinctrl_free_gpio(unsigned gpio);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // GPIO释放函数,...(与 <span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">pinctrl_request_gpio </span>互斥)</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_gpio_direction_input(unsigned gpio);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 设置PIN方向-输入</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_gpio_direction_output(unsigned gpio);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // 设置PIN方向-输出</span></span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern struct pinctrl * __must_check pinctrl_get(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 获取PIN的句柄</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern void pinctrl_put(struct pinctrl *p);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 释放...(与 <span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">pinctrl_get </span>互斥)</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern struct pinctrl_state * __must_check pinctrl_lookup_state(</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                            struct pinctrl *p,</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                            const char *name);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 根据名字查找 <span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">pinctrl_state ,查找不到则创建一个</span></span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_select_state(struct pinctrl *p, struct pinctrl_state *s);</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern struct pinctrl * __must_check devm_pinctrl_get(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // <span style="font-family: Arial; color: rgb(50, 50, 50);">devm_pinctrl_get是Resource managed版本的pinctrl_get，核心还是pinctrl_get函数</span></span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern void devm_pinctrl_put(struct pinctrl *p);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // <span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">释放...(与<span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">devm_pinctrl_get </span></span><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">互斥)</span></span></span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#ifdef CONFIG_PM</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_pm_select_default_state(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // 设置PIN状态为 default</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_pm_select_sleep_state(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span> // ... sleep</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">extern int pinctrl_pm_select_idle_state(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // ... idle</span></span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#else</span></div><div><font face="Monaco">...</font></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#endif</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#else /* !CONFIG_PINCTRL */</span></div><div><font face="Monaco">...</font></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#endif /* CONFIG_PINCTRL */</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">static inline struct pinctrl * __must_check pinctrl_get_select(</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                    struct device *dev, const char *name)</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">{</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    struct pinctrl *p;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    struct pinctrl_state *s;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    int ret;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    p = pinctrl_get(dev);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (IS_ERR(p))</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return p;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    s = pinctrl_lookup_state(p, name);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (IS_ERR(s)) {</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        pinctrl_put(p);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return ERR_CAST(s);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    }</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    ret = pinctrl_select_state(p, s);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (ret &lt; 0) {</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        pinctrl_put(p);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return ERR_PTR(ret);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    }</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    return p;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">}</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">static inline struct pinctrl * __must_check pinctrl_get_select_default(</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                    struct device *dev)</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">{</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    return pinctrl_get_select(dev, PINCTRL_STATE_DEFAULT);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">}</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">static inline struct pinctrl * __must_check devm_pinctrl_get_select(</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                    struct device *dev, const char *name)</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">{</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    struct pinctrl *p;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    struct pinctrl_state *s;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    int ret;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    p = devm_pinctrl_get(dev);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (IS_ERR(p))</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return p;</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    s = pinctrl_lookup_state(p, name);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (IS_ERR(s)) {</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        devm_pinctrl_put(p);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return ERR_CAST(s);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    }</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    ret = pinctrl_select_state(p, s);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    if (ret &lt; 0) {</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        devm_pinctrl_put(p);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">        return ERR_PTR(ret);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    }</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    return p;</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">}</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">static inline struct pinctrl * __must_check devm_pinctrl_get_select_default(</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">                    struct device *dev)</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">{</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">    return devm_pinctrl_get_select(dev, PINCTRL_STATE_DEFAULT);</span></div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">}</span></div><div>
</div><div><span style="font-family: Monaco; font-size: 9pt; color: rgb(51, 51, 51);">#endif /* __LINUX_PINCTRL_CONSUMER_H */</span></div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3130"/>

# /include/linux/pinctrl/devinfo.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/16 17:12_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/16 17:39_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Per-device information from the pin control system.</div><div>* This is the stuff that get included into the device</div><div>* core.</div><div>*</div><div>* Copyright (C) 2012 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>* This interface is used in the core to keep track of pins.</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>
</div><div>#ifndef PINCTRL_DEVINFO_H</div><div>#define PINCTRL_DEVINFO_H</div><div>
</div><div>#ifdef CONFIG_PINCTRL</div><div>
</div><div>/* The device core acts as a consumer toward pinctrl */</div><div>#include &lt;linux/pinctrl/consumer.h&gt;</div><div>
</div><div>/**</div><div>* struct dev_pin_info - pin state container for devices</div><div>* @p: pinctrl handle for the containing device</div><div>* @default_state: the default state for the handle, if found</div><div>* @init_state: the state at probe time, if found</div><div>* @sleep_state: the state at suspend time, if found</div><div>* @idle_state: the state at idle (runtime suspend) time, if found</div><div>*/</div><div>struct dev_pin_info {</div><div>    struct pinctrl *p;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 该device对应的 PIN 的句柄</span></div><div>    struct pinctrl_state *default_state;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // 该device默认时PIN的状态</span></div><div>    struct pinctrl_state *init_state;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // ...初始化..</span></div><div>#ifdef CONFIG_PM</div><div>    struct pinctrl_state *sleep_state;</div><div>    struct pinctrl_state *idle_state;</div><div>#endif</div><div>};</div><div>
</div><div>extern int pinctrl_bind_pins(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 似乎有几个地方实现,比如:</span>drivers/base/pinctrl.c,根据device获取到该device的PIN,但是具体什么用,目前没有了解</div><div>extern int pinctrl_init_done(struct device *dev);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 把该device的PIN的状态设置为默认???</span></div><div>
</div><div>#else</div><div>
</div><div>/* Stubs if we're not using pinctrl */</div><div>
</div><div>static inline int pinctrl_bind_pins(struct device *dev)</div><div>{</div><div>    return 0;</div><div>}</div><div>
</div><div>static inline int pinctrl_init_done(struct device *dev)</div><div>{</div><div>    return 0;</div><div>}</div><div>
</div><div>#endif /* CONFIG_PINCTRL */</div><div>#endif /* PINCTRL_DEVINFO_H */</div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3132"/>

# /include/linux/pinctrl/machine.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/17 15:35_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/18 18:45_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Machine interface for the pinctrl subsystem.</div><div>*</div><div>* Copyright (C) 2011 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>* Based on bits of regulator core, gpio core and clk core</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>#ifndef __LINUX_PINCTRL_MACHINE_H</div><div>#define __LINUX_PINCTRL_MACHINE_H</div><div>
</div><div>#include &lt;linux/bug.h&gt;</div><div>
</div><div>#include &lt;linux/pinctrl/pinctrl-state.h&gt;</div><div>
</div><div>enum pinctrl_map_type {</div><div>    PIN_MAP_TYPE_INVALID,</div><div>    PIN_MAP_TYPE_DUMMY_STATE,</div><div>    PIN_MAP_TYPE_MUX_GROUP,</div><div>    PIN_MAP_TYPE_CONFIGS_PIN,</div><div>    PIN_MAP_TYPE_CONFIGS_GROUP,</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_map_mux - mapping table content for MAP_TYPE_MUX_GROUP</div><div>* @group: the name of the group whose mux function is to be configured. This</div><div>*    field may be left NULL, and the first applicable group for the function</div><div>*    will be used.</div><div>* @function: the mux function to select for the group</div><div>*/</div><div>struct pinctrl_map_mux {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // </span>当 pinctrl_map 结构成员 type 为 MAP_TYPE_MUX_GROUP 时</div><div>    const char *group;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>      //</span></div><div>    const char *function;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // 具体复用方法的名词???</span></div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_map_configs - mapping table content for MAP_TYPE_CONFIGS_*</div><div>* @group_or_pin: the name of the pin or group whose configuration parameters</div><div>*    are to be configured.</div><div>* @configs: a pointer to an array of config parameters/values to program into</div><div>*    hardware. Each individual pin controller defines the format and meaning</div><div>*    of config parameters.</div><div>* @num_configs: the number of entries in array @configs</div><div>*/</div><div>struct pinctrl_map_configs {                                                             // 当 pinctrl_map 结构成员 type 为 MAP_TYPE_CONFIGS_* 时</div><div>    const char *group_or_pin;                                                            // pin或者pin group的名字,比如:UART/I2C/SPI???</div><div>    unsigned long *configs;                                                              // 这组pin配置成各个功能的配置项数组???由具体驱动着提供???</div><div>    unsigned num_configs;                                                                // configs的数量</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_map - boards/machines shall provide this map for devices</div><div>* @dev_name: the name of the device using this specific mapping, the name</div><div>*    must be the same as in your struct device*. If this name is set to the</div><div>*    same name as the pin controllers own dev_name(), the map entry will be</div><div>*    hogged by the driver itself upon registration</div><div>* @name: the name of this specific map entry for the particular machine.</div><div>*    This is the parameter passed to pinmux_lookup_state()</div><div>* @type: the type of mapping table entry</div><div>* @ctrl_dev_name: the name of the device controlling this specific mapping,</div><div>*    the name must be the same as in your struct device*. This field is not</div><div>*    used for PIN_MAP_TYPE_DUMMY_STATE</div><div>* @data: Data specific to the mapping type</div><div>*/</div><div>struct pinctrl_map {</div><div>    const char *dev_name;                                                                 // 使用这个 map entry 的 struct device 的名字,比如:UART</div><div>    const char *name;                                                                     // map entry 的名字???比如:pinctrl-state.h中的几个状态:default/init/idle/sleep</div><div>    enum pinctrl_map_type type;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // </span></div><div>    const char *ctrl_dev_name;                                                            // pin controller 的名字,应该是一个device</div><div>    union {</div><div>        struct pinctrl_map_mux mux;</div><div>        struct pinctrl_map_configs configs;</div><div>    } data;</div><div>};</div><div>
</div><div>/* Convenience macros to create mapping table entries */</div><div>
</div><div>#define PIN_MAP_DUMMY_STATE(dev, state) \</div><div>    {                                \</div><div>        .dev_name = dev,                    \</div><div>        .name = state,                        \</div><div>        .type = PIN_MAP_TYPE_DUMMY_STATE,            \</div><div>    }</div><div>
</div><div>#define PIN_MAP_MUX_GROUP(dev, state, pinctrl, grp, func)        \</div><div>    {                                \</div><div>        .dev_name = dev,                    \</div><div>        .name = state,                        \</div><div>        .type = PIN_MAP_TYPE_MUX_GROUP,                \</div><div>        .ctrl_dev_name = pinctrl,                \</div><div>        .data.mux = {                        \</div><div>            .group = grp,                    \</div><div>            .function = func,                \</div><div>        },                            \</div><div>    }</div><div>
</div><div>#define PIN_MAP_MUX_GROUP_DEFAULT(dev, pinctrl, grp, func)        \</div><div>    PIN_MAP_MUX_GROUP(dev, PINCTRL_STATE_DEFAULT, pinctrl, grp, func)</div><div>
</div><div>#define PIN_MAP_MUX_GROUP_HOG(dev, state, grp, func)            \</div><div>    PIN_MAP_MUX_GROUP(dev, state, dev, grp, func)</div><div>
</div><div>#define PIN_MAP_MUX_GROUP_HOG_DEFAULT(dev, grp, func)            \</div><div>    PIN_MAP_MUX_GROUP(dev, PINCTRL_STATE_DEFAULT, dev, grp, func)</div><div>
</div><div>#define PIN_MAP_CONFIGS_PIN(dev, state, pinctrl, pin, cfgs)        \</div><div>    {                                \</div><div>        .dev_name = dev,                    \</div><div>        .name = state,                        \</div><div>        .type = PIN_MAP_TYPE_CONFIGS_PIN,            \</div><div>        .ctrl_dev_name = pinctrl,                \</div><div>        .data.configs = {                    \</div><div>            .group_or_pin = pin,                \</div><div>            .configs = cfgs,                \</div><div>            .num_configs = ARRAY_SIZE(cfgs),        \</div><div>        },                            \</div><div>    }</div><div>
</div><div>#define PIN_MAP_CONFIGS_PIN_DEFAULT(dev, pinctrl, pin, cfgs)        \</div><div>    PIN_MAP_CONFIGS_PIN(dev, PINCTRL_STATE_DEFAULT, pinctrl, pin, cfgs)</div><div>
</div><div>#define PIN_MAP_CONFIGS_PIN_HOG(dev, state, pin, cfgs)            \</div><div>    PIN_MAP_CONFIGS_PIN(dev, state, dev, pin, cfgs)</div><div>
</div><div>#define PIN_MAP_CONFIGS_PIN_HOG_DEFAULT(dev, pin, cfgs)            \</div><div>    PIN_MAP_CONFIGS_PIN(dev, PINCTRL_STATE_DEFAULT, dev, pin, cfgs)</div><div>
</div><div>#define PIN_MAP_CONFIGS_GROUP(dev, state, pinctrl, grp, cfgs)        \</div><div>    {                                \</div><div>        .dev_name = dev,                    \</div><div>        .name = state,                        \</div><div>        .type = PIN_MAP_TYPE_CONFIGS_GROUP,            \</div><div>        .ctrl_dev_name = pinctrl,                \</div><div>        .data.configs = {                    \</div><div>            .group_or_pin = grp,                \</div><div>            .configs = cfgs,                \</div><div>            .num_configs = ARRAY_SIZE(cfgs),        \</div><div>        },                            \</div><div>    }</div><div>
</div><div>#define PIN_MAP_CONFIGS_GROUP_DEFAULT(dev, pinctrl, grp, cfgs)        \</div><div>    PIN_MAP_CONFIGS_GROUP(dev, PINCTRL_STATE_DEFAULT, pinctrl, grp, cfgs)</div><div>
</div><div>#define PIN_MAP_CONFIGS_GROUP_HOG(dev, state, grp, cfgs)        \</div><div>    PIN_MAP_CONFIGS_GROUP(dev, state, dev, grp, cfgs)</div><div>
</div><div>#define PIN_MAP_CONFIGS_GROUP_HOG_DEFAULT(dev, grp, cfgs)        \</div><div>    PIN_MAP_CONFIGS_GROUP(dev, PINCTRL_STATE_DEFAULT, dev, grp, cfgs)</div><div>
</div><div>#ifdef CONFIG_PINCTRL</div><div>
</div><div>extern int pinctrl_register_mappings(struct pinctrl_map const *map,</div><div>                unsigned num_maps);                                                        // machine初始化的时候把所有的 PIN 都注册到pin subsystem里???</div><div>extern void pinctrl_provide_dummies(void);</div><div>#else</div><div>...</div><div>#endif /* !CONFIG_PINCTRL */</div><div>#endif</div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3134"/>

# /include/linux/pinctrl/pinctrl.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/17 18:16_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/18 20:01_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Interface the pinctrl subsystem</div><div>*</div><div>* Copyright (C) 2011 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>* This interface is used in the core to keep track of pins.</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>#ifndef __LINUX_PINCTRL_PINCTRL_H</div><div>#define __LINUX_PINCTRL_PINCTRL_H</div><div>
</div><div>#ifdef CONFIG_PINCTRL</div><div>
</div><div>#include &lt;linux/radix-tree.h&gt;</div><div>#include &lt;linux/list.h&gt;</div><div>#include &lt;linux/seq_file.h&gt;</div><div>#include &lt;linux/pinctrl/pinctrl-state.h&gt;</div><div>
</div><div>struct device;</div><div>struct pinctrl_dev;</div><div>struct pinctrl_map;</div><div>struct pinmux_ops;</div><div>struct pinconf_ops;</div><div>struct pin_config_item;</div><div>struct gpio_chip;</div><div>struct device_node;</div><div>
</div><div>/**</div><div>* struct pinctrl_pin_desc - boards/machines provide information on their</div><div>* pins, pads or other muxable units in this struct</div><div>* @number: unique pin number from the global pin number space</div><div>* @name: a name for this pin</div><div>* @drv_data: driver-defined per-pin data. pinctrl core does not touch this</div><div>*/</div><div>struct pinctrl_pin_desc {                                                                           // 单个PIN的描述</div><div>    unsigned number;                                                                                // 单个PIN的编号,由具体驱动工程师确定</div><div>    const char *name;                                                                               // ...名字...</div><div>    void *drv_data;                                                                                 // 私有数据</div><div>};</div><div>
</div><div>/* Convenience macro to define a single named or anonymous pin descriptor */</div><div>#define PINCTRL_PIN(a, b) { .number = a, .name = b }</div><div>#define PINCTRL_PIN_ANON(a) { .number = a }</div><div>
</div><div>/**</div><div>* struct pinctrl_gpio_range - each pin controller can provide subranges of</div><div>* the GPIO number space to be handled by the controller</div><div>* @node: list node for internal use</div><div>* @name: a name for the chip in this range</div><div>* @id: an ID number for the chip in this range</div><div>* @base: base offset of the GPIO range</div><div>* @pin_base: base pin number of the GPIO range if pins == NULL</div><div>* @pins: enumeration of pins in GPIO range or NULL</div><div>* @npins: number of pins in the GPIO range, including the base number</div><div>* @gc: an optional pointer to a gpio_chip</div><div>*/</div><div>struct pinctrl_gpio_range {</div><div>    struct list_head node;                                                                          // 被所属 struct pinctrl_dev 管理</div><div>    const char *name;</div><div>    unsigned int id;</div><div>    unsigned int base;                                                                              // GPIO起始ID</div><div>    unsigned int pin_base;                                                                          // 线性(规律)PIN起始ID</div><div>    unsigned const *pins;                                                                           // 非线性(无规律)GPIO-&gt;PIN映射</div><div>    unsigned int npins;                                                                             // 该RANGE数量</div><div>    struct gpio_chip *gc;</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_ops - global pin control operations, to be implemented by</div><div>* pin controller drivers.</div><div>* @get_groups_count: Returns the count of total number of groups registered.</div><div>* @get_group_name: return the group name of the pin group</div><div>* @get_group_pins: return an array of pins corresponding to a certain</div><div>*    group selector @pins, and the size of the array in @num_pins</div><div>* @pin_dbg_show: optional debugfs display hook that will provide per-device</div><div>*    info for a certain pin in debugfs</div><div>* @dt_node_to_map: parse a device tree &quot;pin configuration node&quot;, and create</div><div>*    mapping table entries for it. These are returned through the @map and</div><div>*    @num_maps output parameters. This function is optional, and may be</div><div>*    omitted for pinctrl drivers that do not support device tree.</div><div>* @dt_free_map: free mapping table entries created via @dt_node_to_map. The</div><div>*    top-level @map pointer must be freed, along with any dynamically</div><div>*    allocated members of the mapping table entries themselves. This</div><div>*    function is optional, and may be omitted for pinctrl drivers that do</div><div>*    not support device tree.</div><div>*/</div><div>struct pinctrl_ops {</div><div>    int (*get_groups_count) (struct pinctrl_dev *pctldev);                                          // 获取这个PIN GROUP中PIN的数量</div><div>    const char *(*get_group_name) (struct pinctrl_dev *pctldev,</div><div>                       unsigned selector);                                                          // 根据selector(索引???)获取这个PIN具体GROUP的名称</div><div>    int (*get_group_pins) (struct pinctrl_dev *pctldev,</div><div>                   unsigned selector,</div><div>                   const unsigned **pins,</div><div>                   unsigned *num_pins);                                                             // 根据selector(索引???)获取这个PIN具体GROUP的所以PIN</div><div>    void (*pin_dbg_show) (struct pinctrl_dev *pctldev, struct seq_file *s,</div><div>              unsigned offset);                                                                     // ???</div><div>    int (*dt_node_to_map) (struct pinctrl_dev *pctldev,</div><div>                   struct device_node *np_config,</div><div>                   struct pinctrl_map **map, unsigned *num_maps);                                   // ???</div><div>    void (*dt_free_map) (struct pinctrl_dev *pctldev,</div><div>                 struct pinctrl_map *map, unsigned num_maps);                                       // ???</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_desc - pin controller descriptor, register this to pin</div><div>* control subsystem</div><div>* @name: name for the pin controller</div><div>* @pins: an array of pin descriptors describing all the pins handled by</div><div>*    this pin controller</div><div>* @npins: number of descriptors in the array, usually just ARRAY_SIZE()</div><div>*    of the pins field above</div><div>* @pctlops: pin control operation vtable, to support global concepts like</div><div>*    grouping of pins, this is optional.</div><div>* @pmxops: pinmux operations vtable, if you support pinmuxing in your driver</div><div>* @confops: pin config operations vtable, if you support pin configuration in</div><div>*    your driver</div><div>* @owner: module providing the pin controller, used for refcounting</div><div>* @num_custom_params: Number of driver-specific custom parameters to be parsed</div><div>*    from the hardware description</div><div>* @custom_params: List of driver_specific custom parameters to be parsed from</div><div>*    the hardware description</div><div>* @custom_conf_items: Information how to print @params in debugfs, must be</div><div>*    the same size as the @custom_params, i.e. @num_custom_params</div><div>*/</div><div>struct pinctrl_desc {                                                                                // PIN控制器描述符,每个特定的 PIN控制器 都用一个 struct pinctrl_desc 来描述</div><div>    const char *name;                                                                                // </div><div>    const struct pinctrl_pin_desc *pins;                                                             // 具体每个PIN的描述</div><div>    unsigned int npins;                                                                              // 这个PIN控制器控制PIN的数量</div><div>    const struct pinctrl_ops *pctlops;                                                               // </div><div>    const struct pinmux_ops *pmxops;                                                                 // PIN复用操作函数</div><div>    const struct pinconf_ops *confops;                                                               // PIN配置操作函数,比如:上拉/下拉</div><div>    struct module *owner;</div><div>#ifdef CONFIG_GENERIC_PINCONF</div><div>    unsigned int num_custom_params;</div><div>    const struct pinconf_generic_params *custom_params;</div><div>    const struct pin_config_item *custom_conf_items;</div><div>#endif</div><div>};</div><div>
</div><div>/* External interface to pin controller */</div><div>extern struct pinctrl_dev *pinctrl_register(struct pinctrl_desc *pctldesc,</div><div>                struct device *dev, void *driver_data);</div><div>extern void pinctrl_unregister(struct pinctrl_dev *pctldev);</div><div>extern bool pin_is_valid(struct pinctrl_dev *pctldev, int pin);</div><div>extern void pinctrl_add_gpio_range(struct pinctrl_dev *pctldev,</div><div>                struct pinctrl_gpio_range *range);</div><div>extern void pinctrl_add_gpio_ranges(struct pinctrl_dev *pctldev,</div><div>                struct pinctrl_gpio_range *ranges,</div><div>                unsigned nranges);</div><div>extern void pinctrl_remove_gpio_range(struct pinctrl_dev *pctldev,</div><div>                struct pinctrl_gpio_range *range);</div><div>
</div><div>extern struct pinctrl_dev *pinctrl_find_and_add_gpio_range(const char *devname,</div><div>        struct pinctrl_gpio_range *range);</div><div>extern struct pinctrl_gpio_range *</div><div>pinctrl_find_gpio_range_from_pin(struct pinctrl_dev *pctldev,</div><div>                 unsigned int pin);</div><div>extern int pinctrl_get_group_pins(struct pinctrl_dev *pctldev,</div><div>                const char *pin_group, const unsigned **pins,</div><div>                unsigned *num_pins);</div><div>
</div><div>#ifdef CONFIG_OF</div><div>extern struct pinctrl_dev *of_pinctrl_get(struct device_node *np);</div><div>#else</div><div>static inline</div><div>struct pinctrl_dev *of_pinctrl_get(struct device_node *np)</div><div>{</div><div>    return NULL;</div><div>}</div><div>#endif /* CONFIG_OF */</div><div>
</div><div>extern const char *pinctrl_dev_get_name(struct pinctrl_dev *pctldev);</div><div>extern const char *pinctrl_dev_get_devname(struct pinctrl_dev *pctldev);</div><div>extern void *pinctrl_dev_get_drvdata(struct pinctrl_dev *pctldev);</div><div>#else</div><div>
</div><div>struct pinctrl_dev;</div><div>
</div><div>/* Sufficiently stupid default functions when pinctrl is not in use */</div><div>static inline bool pin_is_valid(struct pinctrl_dev *pctldev, int pin)</div><div>{</div><div>    return pin &gt;= 0;</div><div>}</div><div>
</div><div>#endif /* !CONFIG_PINCTRL */</div><div>
</div><div>#endif /* __LINUX_PINCTRL_PINCTRL_H */</div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3136"/>

# /include/linux/pinctrl/pinmux.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/17 18:28_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/17 18:33_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Interface the pinmux subsystem</div><div>*</div><div>* Copyright (C) 2011 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>* Based on bits of regulator core, gpio core and clk core</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>#ifndef __LINUX_PINCTRL_PINMUX_H</div><div>#define __LINUX_PINCTRL_PINMUX_H</div><div>
</div><div>#include &lt;linux/list.h&gt;</div><div>#include &lt;linux/seq_file.h&gt;</div><div>#include &lt;linux/pinctrl/pinctrl.h&gt;</div><div>
</div><div>#ifdef CONFIG_PINMUX</div><div>
</div><div>struct pinctrl_dev;</div><div>
</div><div>/**</div><div>* struct pinmux_ops - pinmux operations, to be implemented by pin controller</div><div>* drivers that support pinmuxing</div><div>* @request: called by the core to see if a certain pin can be made</div><div>*    available for muxing. This is called by the core to acquire the pins</div><div>*    before selecting any actual mux setting across a function. The driver</div><div>*    is allowed to answer &quot;no&quot; by returning a negative error code</div><div>* @free: the reverse function of the request() callback, frees a pin after</div><div>*    being requested</div><div>* @get_functions_count: returns number of selectable named functions available</div><div>*    in this pinmux driver</div><div>* @get_function_name: return the function name of the muxing selector,</div><div>*    called by the core to figure out which mux setting it shall map a</div><div>*    certain device to</div><div>* @get_function_groups: return an array of groups names (in turn</div><div>*    referencing pins) connected to a certain function selector. The group</div><div>*    name can be used with the generic @pinctrl_ops to retrieve the</div><div>*    actual pins affected. The applicable groups will be returned in</div><div>*    @groups and the number of groups in @num_groups</div><div>* @set_mux: enable a certain muxing function with a certain pin group. The</div><div>*    driver does not need to figure out whether enabling this function</div><div>*    conflicts some other use of the pins in that group, such collisions</div><div>*    are handled by the pinmux subsystem. The @func_selector selects a</div><div>*    certain function whereas @group_selector selects a certain set of pins</div><div>*    to be used. On simple controllers the latter argument may be ignored</div><div>* @gpio_request_enable: requests and enables GPIO on a certain pin.</div><div>*    Implement this only if you can mux every pin individually as GPIO. The</div><div>*    affected GPIO range is passed along with an offset(pin number) into that</div><div>*    specific GPIO range - function selectors and pin groups are orthogonal</div><div>*    to this, the core will however make sure the pins do not collide.</div><div>* @gpio_disable_free: free up GPIO muxing on a certain pin, the reverse of</div><div>*    @gpio_request_enable</div><div>* @gpio_set_direction: Since controllers may need different configurations</div><div>*    depending on whether the GPIO is configured as input or output,</div><div>*    a direction selector function may be implemented as a backing</div><div>*    to the GPIO controllers that need pin muxing.</div><div>* @strict: do not allow simultaneous use of the same pin for GPIO and another</div><div>*    function. Check both gpio_owner and mux_owner strictly before approving</div><div>*    the pin request.</div><div>*/</div><div>struct pinmux_ops {</div><div>    int (*request) (struct pinctrl_dev *pctldev, unsigned offset);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 需要复用之前需要请求确认</span></div><div>    int (*free) (struct pinctrl_dev *pctldev, unsigned offset);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // </span>request的逆函数</div><div>    int (*get_functions_count) (struct pinctrl_dev *pctldev);</div><div>    const char *(*get_function_name) (struct pinctrl_dev *pctldev,</div><div>                      unsigned selector);</div><div>    int (*get_function_groups) (struct pinctrl_dev *pctldev,</div><div>                  unsigned selector,</div><div>                  const char * const **groups,</div><div>                  unsigned *num_groups);</div><div>    int (*set_mux) (struct pinctrl_dev *pctldev, unsigned func_selector,</div><div>            unsigned group_selector);</div><div>    int (*gpio_request_enable) (struct pinctrl_dev *pctldev,</div><div>                    struct pinctrl_gpio_range *range,</div><div>                    unsigned offset);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 请求并使能</span></div><div>    void (*gpio_disable_free) (struct pinctrl_dev *pctldev,</div><div>                   struct pinctrl_gpio_range *range,</div><div>                   unsigned offset);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // </span>gpio_request_enable的逆函数</div><div>    int (*gpio_set_direction) (struct pinctrl_dev *pctldev,</div><div>                   struct pinctrl_gpio_range *range,</div><div>                   unsigned offset,</div><div>                   bool input);<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // 设置某个或多个PIN的输入/输出方向</span></div><div>    bool strict;</div><div>};</div><div>
</div><div>#endif /* CONFIG_PINMUX */</div><div>
</div><div>#endif /* __LINUX_PINCTRL_PINMUX_H */</div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3138"/>

# /include/linux/pinctrl/pinconf.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/17 18:34_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/17 18:35_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Interface the pinconfig portions of the pinctrl subsystem</div><div>*</div><div>* Copyright (C) 2011 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>* This interface is used in the core to keep track of pins.</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>#ifndef __LINUX_PINCTRL_PINCONF_H</div><div>#define __LINUX_PINCTRL_PINCONF_H</div><div>
</div><div>#ifdef CONFIG_PINCONF</div><div>
</div><div>#include &lt;linux/pinctrl/machine.h&gt;</div><div>
</div><div>struct pinctrl_dev;</div><div>struct seq_file;</div><div>
</div><div>/**</div><div>* struct pinconf_ops - pin config operations, to be implemented by</div><div>* pin configuration capable drivers.</div><div>* @is_generic: for pin controllers that want to use the generic interface,</div><div>*    this flag tells the framework that it's generic.</div><div>* @pin_config_get: get the config of a certain pin, if the requested config</div><div>*    is not available on this controller this should return -ENOTSUPP</div><div>*    and if it is available but disabled it should return -EINVAL</div><div>* @pin_config_set: configure an individual pin</div><div>* @pin_config_group_get: get configurations for an entire pin group</div><div>* @pin_config_group_set: configure all pins in a group</div><div>* @pin_config_dbg_parse_modify: optional debugfs to modify a pin configuration</div><div>* @pin_config_dbg_show: optional debugfs display hook that will provide</div><div>*    per-device info for a certain pin in debugfs</div><div>* @pin_config_group_dbg_show: optional debugfs display hook that will provide</div><div>*    per-device info for a certain group in debugfs</div><div>* @pin_config_config_dbg_show: optional debugfs display hook that will decode</div><div>*    and display a driver's pin configuration parameter</div><div>*/</div><div>struct pinconf_ops {</div><div>#ifdef CONFIG_GENERIC_PINCONF</div><div>    bool is_generic;</div><div>#endif</div><div>    int (*pin_config_get) (struct pinctrl_dev *pctldev,</div><div>                   unsigned pin,</div><div>                   unsigned long *config);</div><div>    int (*pin_config_set) (struct pinctrl_dev *pctldev,</div><div>                   unsigned pin,</div><div>                   unsigned long *configs,</div><div>                   unsigned num_configs);</div><div>    int (*pin_config_group_get) (struct pinctrl_dev *pctldev,</div><div>                     unsigned selector,</div><div>                     unsigned long *config);</div><div>    int (*pin_config_group_set) (struct pinctrl_dev *pctldev,</div><div>                     unsigned selector,</div><div>                     unsigned long *configs,</div><div>                     unsigned num_configs);</div><div>    int (*pin_config_dbg_parse_modify) (struct pinctrl_dev *pctldev,</div><div>                       const char *arg,</div><div>                       unsigned long *config);</div><div>    void (*pin_config_dbg_show) (struct pinctrl_dev *pctldev,</div><div>                     struct seq_file *s,</div><div>                     unsigned offset);</div><div>    void (*pin_config_group_dbg_show) (struct pinctrl_dev *pctldev,</div><div>                       struct seq_file *s,</div><div>                       unsigned selector);</div><div>    void (*pin_config_config_dbg_show) (struct pinctrl_dev *pctldev,</div><div>                        struct seq_file *s,</div><div>                        unsigned long config);</div><div>};</div><div>
</div><div>#endif</div><div>
</div><div>#endif /* __LINUX_PINCTRL_PINCONF_H */</div><div>
</div></div><div>
</div></span>
</div>

* * *

<a name="3142"/>

# /drivers/pinctrl/core.h

<div>
<table bgcolor="#D4DDE5" border="0">
<tr><td>**创建时间：**</td><td>_2019/2/18 19:39_</td></tr>
<tr><td>**更新时间：**</td><td>_2019/2/18 20:38_</td></tr>
<tr><td>**作者：**</td><td>_linyuanbo2004@163.com_</td></tr>
</table>
</div>

<div>
<span><div>
</div><div style="box-sizing: border-box; padding: 8px; font-family: Monaco, Menlo, Consolas, &quot;Courier New&quot;, monospace; font-size: 12px; color: rgb(51, 51, 51); border-radius: 4px; background-color: rgb(251, 250, 248); border: 1px solid rgba(0, 0, 0, 0.15);-en-codeblock:true;"><div>/*</div><div>* Core private header for the pin control subsystem</div><div>*</div><div>* Copyright (C) 2011 ST-Ericsson SA</div><div>* Written on behalf of Linaro for ST-Ericsson</div><div>*</div><div>* Author: Linus Walleij &lt;linus.walleij@linaro.org&gt;</div><div>*</div><div>* License terms: GNU General Public License (GPL) version 2</div><div>*/</div><div>
</div><div>#include &lt;linux/kref.h&gt;</div><div>#include &lt;linux/mutex.h&gt;</div><div>#include &lt;linux/radix-tree.h&gt;</div><div>#include &lt;linux/pinctrl/pinconf.h&gt;</div><div>#include &lt;linux/pinctrl/machine.h&gt;</div><div>
</div><div>struct pinctrl_gpio_range;</div><div>
</div><div>/**</div><div>* struct pinctrl_dev - pin control class device</div><div>* @node: node to include this pin controller in the global pin controller list</div><div>* @desc: the pin controller descriptor supplied when initializing this pin</div><div>*    controller</div><div>* @pin_desc_tree: each pin descriptor for this pin controller is stored in</div><div>*    this radix tree</div><div>* @gpio_ranges: a list of GPIO ranges that is handled by this pin controller,</div><div>*    ranges are added to this list at runtime</div><div>* @dev: the device entry for this pin controller</div><div>* @owner: module providing the pin controller, used for refcounting</div><div>* @driver_data: driver data for drivers registering to the pin controller</div><div>*    subsystem</div><div>* @p: result of pinctrl_get() for this device</div><div>* @hog_default: default state for pins hogged by this device</div><div>* @hog_sleep: sleep state for pins hogged by this device</div><div>* @mutex: mutex taken on each pin controller specific action</div><div>* @device_root: debugfs root for this device</div><div>*/</div><div>struct pinctrl_dev {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // PIN device</span></div><div>    struct list_head node;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 该 PIN</span>(device) <span>控制链表节点,由全局链表 </span>pinctrldev_list(在对应core.c中) <span>管理</span></div><div>    struct pinctrl_desc *desc;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // 该 PIN(device) 对应的PIN控制器</span></div><div>    struct radix_tree_root pin_desc_tree;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     //</span> 该 PIN(device) <span>设备树描述节点???</span></div><div>    struct list_head gpio_ranges;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 管理 </span>struct pinctrl_gpio_range 中成员node子节点</div><div>    struct device *dev;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>       // ...</span></div><div>    struct module *owner;</div><div>    void *driver_data;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>        // 私有数据</span></div><div>    struct pinctrl *p;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // </span></div><div>    struct pinctrl_state *hog_default;</div><div>    struct pinctrl_state *hog_sleep;</div><div>    struct mutex mutex;</div><div>#ifdef CONFIG_DEBUG_FS</div><div>    struct dentry *device_root;</div><div>#endif</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl - per-device pin control state holder</div><div>* @node: global list node</div><div>* @dev: the device using this pin control handle</div><div>* @states: a list of states for this device</div><div>* @state: the current state</div><div>* @dt_maps: the mapping table chunks dynamically parsed from device tree for</div><div>*    this device, if any</div><div>* @users: reference count</div><div>*/</div><div>struct pinctrl {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // 一组或者一个PIN的句柄???</span></div><div>    struct list_head node;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // </span>该 PIN(device) 控制链表节点,由全局链表 pinctrl_list(在对应core.c中) 管理</div><div>    struct device *dev;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // ...</span></div><div>    struct list_head states;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  // </span>管理 struct pinctrl_state 中成员node子节点</div><div>    struct pinctrl_state *state;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>      // 当前的状态???</span></div><div>    struct list_head dt_maps;</div><div>    struct kref users;</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_state - a pinctrl state for a device</div><div>* @node: list node for struct pinctrl's @states field</div><div>* @name: the name of this state</div><div>* @settings: a list of settings for this state</div><div>*/</div><div>struct pinctrl_state {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>    struct list_head node;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // </span>该 STATE(device) 控制链表节点,由它所属链表 struct pinctrl中states 管理</div><div>    const char *name;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // </span></div><div>    struct list_head settings;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    // </span>管理 struct pinctrl_setting 中成员node子节点</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_setting_mux - setting data for MAP_TYPE_MUX_GROUP</div><div>* @group: the group selector to program</div><div>* @func: the function selector to program</div><div>*/</div><div>struct pinctrl_setting_mux {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>      // </span>当 struct pinctrl_setting中enum pinctrl_map_type type 结构成员 type 为 MAP_TYPE_MUX_GROUP 时</div><div>    unsigned group;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>       //</span></div><div>    unsigned func;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_setting_configs - setting data for MAP_TYPE_CONFIGS_*</div><div>* @group_or_pin: the group selector or pin ID to program</div><div>* @configs: a pointer to an array of config parameters/values to program into</div><div>*    hardware. Each individual pin controller defines the format and meaning</div><div>*    of config parameters.</div><div>* @num_configs: the number of entries in array @configs</div><div>*/</div><div>struct pinctrl_setting_configs {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // </span>当 struct pinctrl_setting中enum pinctrl_map_type type 结构成员 type 为 MAP_TYPE_CONFIGS_* 时</div><div>    unsigned group_or_pin;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   //</span></div><div>    unsigned long *configs;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>      //</span></div><div>    unsigned num_configs;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_setting - an individual mux or config setting</div><div>* @node: list node for struct pinctrl_settings's @settings field</div><div>* @type: the type of setting</div><div>* @pctldev: pin control device handling to be programmed. Not used for</div><div>*   PIN_MAP_TYPE_DUMMY_STATE.</div><div>* @dev_name: the name of the device using this state</div><div>* @data: Data specific to the setting type</div><div>*/</div><div>struct pinctrl_setting {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     //</span></div><div>    struct list_head node;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   // </span>该 STATE(device) 控制链表节点,由它所属链表 struct pinctrl_state中settings 管理</div><div>    enum pinctrl_map_type type;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  //</span></div><div>    struct pinctrl_dev *pctldev;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>     // 指向它所属的 </span>struct pinctrl_dev ???</div><div>    const char *dev_name;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>    union {</div><div>        struct pinctrl_setting_mux mux;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  //</span></div><div>        struct pinctrl_setting_configs configs;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>  //</span></div><div>    } data;</div><div>};</div><div>
</div><div>/**</div><div>* struct pin_desc - pin descriptor for each physical pin in the arch</div><div>* @pctldev: corresponding pin control device</div><div>* @name: a name for the pin, e.g. the name of the pin/pad/finger on a</div><div>*    datasheet or such</div><div>* @dynamic_name: if the name of this pin was dynamically allocated</div><div>* @mux_usecount: If zero, the pin is not claimed, and @owner should be NULL.</div><div>*    If non-zero, this pin is claimed by @owner. This field is an integer</div><div>*    rather than a boolean, since pinctrl_get() might process multiple</div><div>*    mapping table entries that refer to, and hence claim, the same group</div><div>*    or pin, and each of these will increment the @usecount.</div><div>* @mux_owner: The name of device that called pinctrl_get().</div><div>* @mux_setting: The most recent selected mux setting for this pin, if any.</div><div>* @gpio_owner: If pinctrl_request_gpio() was called for this pin, this is</div><div>*    the name of the GPIO that &quot;owns&quot; this pin.</div><div>*/</div><div>struct pin_desc {<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>    struct pinctrl_dev *pctldev;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span> //</span></div><div>    const char *name;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    //</span></div><div>    bool dynamic_name;<span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>    </span><span>   //</span></div><div>    /* These fields only added when supporting pinmux drivers */</div><div>#ifdef CONFIG_PINMUX</div><div>    unsigned mux_usecount;</div><div>    const char *mux_owner;</div><div>    const struct pinctrl_setting_mux *mux_setting;</div><div>    const char *gpio_owner;</div><div>#endif</div><div>};</div><div>
</div><div>/**</div><div>* struct pinctrl_maps - a list item containing part of the mapping table</div><div>* @node: mapping table list node</div><div>* @maps: array of mapping table entries</div><div>* @num_maps: the number of entries in @maps</div><div>*/</div><div>struct pinctrl_maps {</div><div>    struct list_head node;</div><div>    struct pinctrl_map const *maps;</div><div>    unsigned num_maps;</div><div>};</div><div>
</div><div>struct pinctrl_dev *get_pinctrl_dev_from_devname(const char *dev_name);</div><div>struct pinctrl_dev *get_pinctrl_dev_from_of_node(struct device_node *np);</div><div>int pin_get_from_name(struct pinctrl_dev *pctldev, const char *name);</div><div>const char *pin_get_name(struct pinctrl_dev *pctldev, const unsigned pin);</div><div>int pinctrl_get_group_selector(struct pinctrl_dev *pctldev,</div><div>                   const char *pin_group);</div><div>
</div><div>static inline struct pin_desc *pin_desc_get(struct pinctrl_dev *pctldev,</div><div>                        unsigned int pin)</div><div>{</div><div>    return radix_tree_lookup(&amp;pctldev-&gt;pin_desc_tree, pin);</div><div>}</div><div>
</div><div>extern struct pinctrl_gpio_range *</div><div>pinctrl_find_gpio_range_from_pin_nolock(struct pinctrl_dev *pctldev,</div><div>                    unsigned int pin);</div><div>
</div><div>int pinctrl_register_map(struct pinctrl_map const *maps, unsigned num_maps,</div><div>             bool dup);</div><div>void pinctrl_unregister_map(struct pinctrl_map const *map);</div><div>
</div><div>extern int pinctrl_force_sleep(struct pinctrl_dev *pctldev);</div><div>extern int pinctrl_force_default(struct pinctrl_dev *pctldev);</div><div>
</div><div>extern struct mutex pinctrl_maps_mutex;</div><div>extern struct list_head pinctrl_maps;</div><div>
</div><div>#define for_each_maps(_maps_node_, _i_, _map_) \</div><div>    list_for_each_entry(_maps_node_, &amp;pinctrl_maps, node) \</div><div>        for (_i_ = 0, _map_ = &amp;_maps_node_-&gt;maps[_i_]; \</div><div>            _i_ &lt; _maps_node_-&gt;num_maps; \</div><div>            _i_++, _map_ = &amp;_maps_node_-&gt;maps[_i_])</div><div>
</div></div><div>
</div></span>
</div></body></html> 
