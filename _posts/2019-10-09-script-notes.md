---
layout: mypost
title: 高效能程序员的修炼
categories: [编码笔记,xshell,secureCRT]
---

## Script MISC

---

1. xshell vbs script

    建议使用本地帮助文档，如果没有本地文档可以使用在线文档：[online](https://netsarang.atlassian.net/wiki/spaces/ENSUP/overview)。

    example :

    ```vbs
    xsh.Screen.Synchronous = True

    Sub Main
        ' send crlf ???
        xsh.Screen.Send VbCr
        ' wait for string "#"yy
        xsh.Screen.WaitForString "#"
        ' send command "ls" + crlf ???
        xsh.Screen.Send "ls" & VbCr
        ' wait for string "#"yy
        xsh.Screen.WaitForString "#"
        ' sleep 1000ms
        xsh.Sleep 1000
        ' after 1000ms send command "ls" + crlf ???
        xsh.Screen.Send "ls" & VbCr
        ' wait for string "#"yy
        xsh.Screen.WaitForString "#"
    End Sub
    ```

2. secureCRT vbs script

    这个工具目前用得比较少了，所以GOOGLE一下吧，

    example :

    ```vbs
    crt.Screen.Synchronous = True

    Sub Main
        ' send crlf ???
        crt.Screen.Send VbCr
        ' wait for string "#"yy
        crt.Screen.WaitForString "#"
        ' send command "ls" + crlf ???
        crt.Screen.Send "ls" & VbCr
        ' wait for string "#"yy
        crt.Screen.WaitForString "#"
        ' sleep 1000ms
        crt.Sleep 1000
        ' after 1000ms send command "ls" + crlf ???
        crt.Screen.Send "ls" & VbCr
        ' wait for string "#"yy
        crt.Screen.WaitForString "#"
    End Sub
    ```
