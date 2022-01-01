---
#layout: mypost
title: 模板模式
categories:
- [设计模式]
- [RTOS]
tags:
- 设计模式
---

维基百科说明：[模板模式](https://en.wikipedia.org/wiki/Template_method_pattern)

> 命令模式（英语：Command pattern）是一种设计模式，它尝试以对象来代表实际行动。命令对象可以把行动(action) 及其参数封装起来，于是这些行动可以被：
>
> - 重复多次
> - 取消（如果该对象有实现的话）
> - 取消后又再重做
>
> 这些都是现代大型应用程序所必须的功能，即“撤销”及“重复”。除此之外，可以用命令模式来实现的功能例子还有：
>
> - 交易行为
> - 进度列
> - 向导
> - 用户界面按钮及功能表项目
> - 线程 pool
> - 宏收录

参考系统：[RT-Thread](https://gitee.com/rtthread/rt-thread)

参考源码版本：[lts-v3.1.x](https://gitee.com/rtthread/rt-thread/tree/lts-v3.1.x)

---

函数名：`static cmd_function_t msh_get_cmd(char *cmd, int size)`

函数理解：根据输入的 `cmd` 匹配相应的命令，返回该命令的函数指针，命令列表与函数指针在 `struct finsh_syscall *index` 中，`FINSH_NEXT_SYSCALL` 的实现与编译器相关。

```c
static cmd_function_t msh_get_cmd(char *cmd, int size)
{
    struct finsh_syscall *index;
    cmd_function_t cmd_func = RT_NULL;

    for (index = _syscall_table_begin;
            index < _syscall_table_end;
            FINSH_NEXT_SYSCALL(index))
    {
        if (strncmp(index->name, "__cmd_", 6) != 0) continue;

        if (strncmp(&index->name[6], cmd, size) == 0 &&
                index->name[6 + size] == '\0')
        {
            cmd_func = (cmd_function_t)index->func;
            break;
        }
    }

    return cmd_func;
}
```

---

理解总结：这种设计模式个人理解就是匹配表用户根据规则输入，通过判断判断条件，执行对应的函数，再次抽象可以实现为，匹配方法由用户自定义，可以是枚举匹配，字符对比，或者内存对比等，调用方法通常传入参数为用户私有数据。
