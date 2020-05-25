---
layout: mypost
title: GPIO-KEY 学习调试笔记
categories: [Linux驱动,设备树]
---

### GPIO-KEY 学习调试笔记

---
硬件平台:[iMX6UL开发板](http://www.topeetboard.com/Product/iMX6UL.html)

u-boot版本:uboot-imx(rel_imx_4.1.15_2.0.0_ga)

kernel版本:linux-imx(rel_imx_4.1.15_2.0.0_ga)

步骤一:修改设备树内容

参考资料:
> linux-imx/Documentation/devicetree/bindings/input/gpio-keys.txt

```dts
/ {
    keys_test {
        compatible = "gpio-keys";
        pinctrl-names = "default";
        pinctrl-0 = <&pinctrl_keys>;

        key-test {
            gpios = <&gpio1 01 GPIO_ACTIVE_LOW>;
            label = "key-test";
            linux,code = <KEY_ESC>;
        };
    };
};

&iomuxc {
    ...
    pinctrl_keys: keygrp {
            fsl,pins = <
                MX6UL_PAD_GPIO1_IO01__GPIO1_IO01        0x17059
            >;
        };
    ...
};
```

步骤二:重新更新设备树之后,进入系统(**确保系统已经使能gpio-keys驱动**)

参考资料:
> linux-imx/Documentation/input/input.txt

```shell
~ # cat /proc/bus/input/devices
...
I: Bus=0019 Vendor=0001 Product=0001 Version=0100
N: Name="keys_test"
P: Phys=gpio-keys/input0
S: Sysfs=/devices/platform/keys_test/input/input3
U: Uniq=
H: Handlers=kbd event2
B: PROP=0
B: EV=3
B: KEY=2
...
~ # ls /dev/input/
event0  event1  event2  mice    mouse0
```

通过查看 `cat /proc/bus/input/devices` 和 `ls /dev/input/` 可以看见新的事件添加

以上步骤完成确认设备树添加已经正确

步骤三:编写测试代码,查看运行结果

参考资料:
> input subsystem programing

```c
#define INPUT_DEV_PATH "/dev/input/event0"

int main(int argc, char **argv)
{
    int fd = 0;
    struct input_event event;
    int ret = 0;

    fd = open(INPUT_DEV_PATH, O_RDONLY);
    assert(fd >= 0);

    printf("goto for\n");
    for (;;)
    {
        memset(&event, 0, sizeof(event));
        ret = read(fd, &event, sizeof(event));
        if (ret < 0)
        {
            printf("read fail ret=[%d]\n", ret);
            exit(1);
        }

        printf("type=[%d], code=[%d], value=[%d]\n", event.type, event.code, event.value);
        printf("sec=[%lu], usec=[%lu]\n", event.time.tv_sec, event.time.tv_usec);
        printf("\n\n\n");
    }

    return 0;
}
```

运行结果:略...
