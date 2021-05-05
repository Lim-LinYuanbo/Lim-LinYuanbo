---
layout: mypost
title: 观察者模式
categories: [设计模式,RTOS,编码笔记]
---

维基百科说明：[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)

观察者模式是软件设计模式的一种。在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实时事件处理系统。

参考系统：[Linux](https://elixir.bootlin.com/linux/v4.1.15/source)

---

函数名：`notifier_chain_register`

函数理解：通知链的注册接口，`nl` 为通知链头，`n` 为通知链具体回调函数实现块

```c
/*
 *  Notifier chain core routines.  The exported routines below
 *  are layered on top of these, with appropriate locking added.
 */

static int notifier_chain_register(struct notifier_block **nl,
            struct notifier_block *n)
{
    while ((*nl) != NULL) {
        if (n->priority > (*nl)->priority)
            break;
        nl = &((*nl)->next);
    }
    n->next = *nl;
    rcu_assign_pointer(*nl, n);
    return 0;
}
```

---

函数名：`notifier_call_chain`

函数理解：通知链触发响应事件接口

```c
/**
 * notifier_call_chain - Informs the registered notifiers about an event.
 *  @nl:        Pointer to head of the blocking notifier chain
 *  @val:        Value passed unmodified to notifier function
 *  @v:        Pointer passed unmodified to notifier function
 *  @nr_to_call:    Number of notifier functions to be called. Don't care
 *          value of this parameter is -1.
 *  @nr_calls:    Records the number of notifications sent. Don't care
 *          value of this field is NULL.
 *  @returns:    notifier_call_chain returns the value returned by the
 *          last notifier function called.
 */
static int notifier_call_chain(struct notifier_block **nl,
                   unsigned long val, void *v,
                   int nr_to_call, int *nr_calls)
{
    int ret = NOTIFY_DONE;
    struct notifier_block *nb, *next_nb;

    nb = rcu_dereference_raw(*nl);

    while (nb && nr_to_call) {
        next_nb = rcu_dereference_raw(nb->next);

#ifdef CONFIG_DEBUG_NOTIFIERS
        if (unlikely(!func_ptr_is_kernel_text(nb->notifier_call))) {
            WARN(1, "Invalid notifier called!");
            nb = next_nb;
            continue;
        }
#endif
        ret = nb->notifier_call(nb, val, v);

        if (nr_calls)
            (*nr_calls)++;

        if ((ret & NOTIFY_STOP_MASK) == NOTIFY_STOP_MASK)
            break;
        nb = next_nb;
        nr_to_call--;
    }
    return ret;
}
NOKPROBE_SYMBOL(notifier_call_chain);
```

---

理解总结：

1. 观察者模式感觉类似与发布订阅的机制，多个订阅者订阅同一个发布者，当发布者状态发生改变，触发/通知订阅者执行自己需要执行的任务。
2. 对于上述接口，订阅者调用 `notifier_chain_register`，发布者状态改变调用 `notifier_call_chain`。
