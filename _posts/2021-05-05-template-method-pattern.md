---
layout: mypost
title: 模板模式
categories: [设计模式,RTOS,编码笔记]
---

维基百科说明：[模板模式](https://en.wikipedia.org/wiki/Template_method_pattern)

模板方法模型是一种行为设计模型。模板方法是一个定义在父类别的方法，在模板方法中会呼叫多个定义在父类别的其他方法，而这些方法有可能只是抽象方法并没有实作，模板方法仅决定这些抽象方法的执行顺序，这些抽象方法的实作由子类别负责，并且子类别不允许覆写模板方法。

参考源码：[nand_base.c](https://elixir.bootlin.com/linux/v4.1.15/source/drivers/mtd/nand/nand_base.c)

---

函数名：`nand_write_oob_std`

函数理解：根据注释，理解为大多数 OOB 数据共同的写入方式函数接口

```c
/**
 * nand_write_oob_std - [REPLACEABLE] the most common OOB data write function
 * @mtd: mtd info structure
 * @chip: nand chip info structure
 * @page: page number to write
 */
static int nand_write_oob_std(struct mtd_info *mtd, struct nand_chip *chip,
                  int page)
{
    int status = 0;
    const uint8_t *buf = chip->oob_poi;
    int length = mtd->oobsize;

    chip->cmdfunc(mtd, NAND_CMD_SEQIN, mtd->writesize, page);
    chip->write_buf(mtd, buf, length);
    /* Send command to program the OOB data */
    chip->cmdfunc(mtd, NAND_CMD_PAGEPROG, -1, -1);

    status = chip->waitfunc(mtd, chip);

    return status & NAND_STATUS_FAIL ? -EIO : 0;
}
```

---

理解总结：

1. 对于一些流程相同的逻辑，把共同的部分抽象出来，把不同的地方暴露给外部，由具体情况具体实现，每次调用这个接口都执行相同的流程。
