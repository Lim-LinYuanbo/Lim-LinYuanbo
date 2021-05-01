---
layout: mypost
title: 面向对象设计
categories: [设计模式,RTOS,编码笔记]
---

维基百科说明：[面向对象设计](https://en.wikipedia.org/wiki/Object-oriented_programming)

面向对象的三大特性：继承、封装、多态，虽然在重要的面向对象编程语言中没有C语言，但是很多代码实现中依然有面向对象设计的思想，以此记录一下学习笔记。

## 继承

维基百科说明：[继承](https://en.wikipedia.org/wiki/Inheritance_(object-oriented_programming))

> 继承（英语：inheritance）是面向对象软件技术当中的一个概念。如果一个类别B“继承自”另一个类别A，就把这个B称为“A的子类”，而把A称为“B的父类别”也可以称“A是B的超类”。继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码。在令子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。另外，为子类追加新的属性和方法也是常见的做法。 一般静态的面向对象编程语言，继承属于静态的，意即在子类的行为在编译期就已经决定，无法在运行期扩展。
>
> 有些编程语言支持多重继承，即一个子类可以同时有多个父类，比如C++编程语言；而在有些编程语言中，一个子类只能继承自一个父类，比如Java编程语言，这时可以透过实现接口来实现与多重继承相似的效果。
>
> 现今面向对象程序设计技巧中，继承并非以继承类别的“行为”为主，而是继承类别的“类型”，使得组件的类型一致。另外在设计模式中提到一个守则，“多用合成，少用继承”，此守则也是用来处理继承无法在运行期动态扩展行为的遗憾。

个人理解：假设有两个相似数据结构，把他们共同的地方抽象出来作为基类，原来两个相似的数据结构包着这个基类数据结构，那么就是简单的继承了，以下是 rt-thread 的实现，多种通信方式都继承了 `struct rt_ipc_object` 这个数据结构。

```c
struct rt_ipc_object
{
    struct rt_object parent;                            /**< inherit from rt_object */

    rt_list_t        suspend_thread;                    /**< threads pended on this resource */
};

struct rt_semaphore
{
    struct rt_ipc_object parent;                        /**< inherit from ipc_object */

    rt_uint16_t          value;                         /**< value of semaphore. */
    rt_uint16_t          reserved;                      /**< reserved field */
};

struct rt_mutex
{
    struct rt_ipc_object parent;                        /**< inherit from ipc_object */

    rt_uint16_t          value;                         /**< value of mutex */

    rt_uint8_t           original_priority;             /**< priority of last thread hold the mutex */
    rt_uint8_t           hold;                          /**< numbers of thread hold the mutex */

    struct rt_thread    *owner;                         /**< current owner of mutex */
};

struct rt_event
{
    struct rt_ipc_object parent;                        /**< inherit from ipc_object */

    rt_uint32_t          set;                           /**< event set */
};

struct rt_mailbox
{
    struct rt_ipc_object parent;                        /**< inherit from ipc_object */

    rt_ubase_t          *msg_pool;                      /**< start address of message buffer */

    rt_uint16_t          size;                          /**< size of message pool */

    rt_uint16_t          entry;                         /**< index of messages in msg_pool */
    rt_uint16_t          in_offset;                     /**< input offset of the message buffer */
    rt_uint16_t          out_offset;                    /**< output offset of the message buffer */

    rt_list_t            suspend_sender_thread;         /**< sender thread suspended on this mailbox */
};

struct rt_messagequeue
{
    struct rt_ipc_object parent;                        /**< inherit from ipc_object */

    void                *msg_pool;                      /**< start address of message queue */

    rt_uint16_t          msg_size;                      /**< message size of each message */
    rt_uint16_t          max_msgs;                      /**< max number of messages */

    rt_uint16_t          entry;                         /**< index of messages in the queue */

    void                *msg_queue_head;                /**< list head */
    void                *msg_queue_tail;                /**< list tail */
    void                *msg_queue_free;                /**< pointer indicated the free node of queue */

    rt_list_t            suspend_sender_thread;         /**< sender thread suspended on this message queue */
};
```

## 封装

维基百科说明：[封装](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))

> 在面向对象编程方法中，封装（英语：Encapsulation）是指，一种将抽象性函数接口的实现细节部分包装、隐藏起来的方法。同时，它也是一种防止外界调用端，去访问对象内部实现细节的手段，这个手段是由编程语言本身来提供的。封装被视为是面向对象的四项原则之一。
>
> 适当的封装，可以将对象使用接口的程序实现部分隐藏起来，不让用户看到，同时确保用户无法任意更改对象内部的重要资料，若想接触资料只能通过公开接入方法（Publicly accessible methods）的方式（ 如："getters" 和"setters"）。它可以让代码更容易理解与维护，也加强了代码的安全性。

个人理解：内核中的原子操作具体实现如下（32位x86平台下atomic API在arch/x86/include/asm/atomic_32.h中实现），虽然C语言没有 private 的结构体成语描述方法，但是在原子操作中，应该也没有人直接访问结构体成员，而是通过系统的接口进行原子操作，具体实现如下：

```c
typedef struct 
{ 
    volatile int counter; 
} 
atomic_t;

#define ATOMIC_INIT(i) { (i) }

static inline int atomic_read(const atomic_t *v)
{
    return v->counter;
}

static inline void atomic_set(atomic_t *v, int i)
{
    v->counter = i;
}
```

## 多态

维基百科说明：[多态](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))

> 在编程语言和类型论中，多态（英语：polymorphism）指为不同数据类型的实体提供统一的接口，或使用一个单一的符号来表示多个不同的类型。

个人理解：各种不同的继承者，调用他们各自不通的实现方式，以 rt-thread 中设备框架模型作为理解，代码实现如下：

所有的设备的基类 `struct rt_device`， 串行数据设备 `struct rt_serial_device`， I2C总线设备 `struct rt_i2c_bus_device`， SPI总线设备 `struct rt_spi_bus`，如下：

```c
struct rt_device
{
    struct rt_object          parent;                   /**< inherit from rt_object */

    enum rt_device_class_type type;                     /**< device type */
    rt_uint16_t               flag;                     /**< device flag */
    rt_uint16_t               open_flag;                /**< device open flag */

    rt_uint8_t                ref_count;                /**< reference count */
    rt_uint8_t                device_id;                /**< 0 - 255 */

    /* device call back */
    rt_err_t (*rx_indicate)(rt_device_t dev, rt_size_t size);
    rt_err_t (*tx_complete)(rt_device_t dev, void *buffer);

#ifdef RT_USING_DEVICE_OPS
    const struct rt_device_ops *ops;
#else
    /* common device interface */
    rt_err_t  (*init)   (rt_device_t dev);
    rt_err_t  (*open)   (rt_device_t dev, rt_uint16_t oflag);
    rt_err_t  (*close)  (rt_device_t dev);
    rt_size_t (*read)   (rt_device_t dev, rt_off_t pos, void *buffer, rt_size_t size);
    rt_size_t (*write)  (rt_device_t dev, rt_off_t pos, const void *buffer, rt_size_t size);
    rt_err_t  (*control)(rt_device_t dev, int cmd, void *args);
#endif

#if defined(RT_USING_POSIX)
    const struct dfs_file_ops *fops;
    struct rt_wqueue wait_queue;
#endif

    void                     *user_data;                /**< device private data */
};

struct rt_serial_device
{
    struct rt_device          parent;

    const struct rt_uart_ops *ops;
    struct serial_configure   config;

    void *serial_rx;
    void *serial_tx;
};

struct rt_i2c_bus_device
{
    struct rt_device parent;
    const struct rt_i2c_bus_device_ops *ops;
    rt_uint16_t  flags;
    rt_uint16_t  addr;
    struct rt_mutex lock;
    rt_uint32_t  timeout;
    rt_uint32_t  retries;
    void *priv;
};

struct rt_spi_bus
{
    struct rt_device parent;
    rt_uint8_t mode;
    const struct rt_spi_ops *ops;

    struct rt_mutex lock;
    struct rt_spi_device *owner;
};
```

在 rt-thread 中，虽然应用层调用一样的方法，但是因为数据结构的不一样，他对于底层实现的方法也不一样，所以实现的功能也不一样，使用方法顺序如下：

1. 通过寻找设备句柄 `rt_device_t rt_device_find(const char* name);`；
2. 打开设备 `rt_err_t rt_device_open(rt_device_t dev, rt_uint16_t oflags);`；
3. 控制设备 `rt_err_t rt_device_control(rt_device_t dev, rt_uint8_t cmd, void* arg);`；
4. 关闭设备 `rt_err_t rt_device_close(rt_device_t dev);`；
