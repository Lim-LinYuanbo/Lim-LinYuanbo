---
layout: mypost
title: MCU-升级文件格式
categories: [编码笔记]
---

## 文件格式

文件信息描述长度:128bytes

| 头部(4bytes) | 版本(4bytes) | 文件大小(4bytes)| 校验(4bytes) | 预留(112bytes) | 升级文件内容(Nbytes) | 尾部(4bytes) |
| --- | --- | --- | --- | --- | --- | --- |
| xxxx | V1.0.0 | 123456 | AA55 | XXXX | xoxoxo | xxxx |
