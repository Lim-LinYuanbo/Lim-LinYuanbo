---
#layout: mypost
title: putty 配色
categories:
- [tools]
tags:
- tools
---

> 不得不说，putty 默认配送的深蓝确实啥也看不见，所以。。。

## 步骤

1. 下载配色文件 [igvita-desert.reg](http://www.igvita.com/downloads/igvita-desert.reg)，通过查看内容，这是一个 windows 注册文件，假如原来已经使用过一些配置并且保存，都会显示在 `[HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions]`。

2. 将 igvita-desert.reg 导入到注册表。

3. 打开 putty 后，读取 igvita-desert.reg 并且重新配置（SSH/TELNET/SERIAL），重命名，保持即可。

## 其他

备注：同时可以修改一下打开窗口的大小（window/columns 和 windows/rows）和缓冲行数（window/line of scollback）
