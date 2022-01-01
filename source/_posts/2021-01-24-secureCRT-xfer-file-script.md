---
#layout: mypost
title: secureCRT 通过脚本发送文件
categories:
- [tools]
tags:
- tools
- 脚本
---

> 由于一次 ECN 引发的事故，工厂生产手动的输入升级命令和选中升级文件以及校对版本实在低效，为了弥补错误，提高效率，需要实现自动化的升级。

## 设备手动流程

1. 设备开机，等待输入命令
2. 输入升级指令
3. 选择升级文件
4. 等待升级结果
5. 升级完成自动重启，查看升级版本

## 设备自动升级流程

1. 设备开机，每隔1s，自动发送回车等待反馈提示符
2. 收到提示符确认成功通信，发送版本查询指令
3. 版本校验，版本与目标版本一致，提示当前固件为最新固件
4. 否则自动输入升级指令，等待接收升级文件反馈
5. 收到接收升级文件反馈，发送升级文件
6. 发送完成回到步骤 1，每隔1s，自动发送回车等待反馈提示符

## secureCRT 帮助文档

1. 消息提示框

    > Description
    >
    >       The Dialog object provides access to simple user-interface features     provided by SecureCRT.
    >
    > Syntax
    >
    > ```crt.Dialog.Method([arglist])```
    >
    > Remarks
    >
    >       SecureCRT's Dialog object is accessed through the top-level object’s    Dialog property.
    >
    > Dialog Object Methods
    >
    >       Methods
    >
    >           FileOpenDialog
    >     
    >           MessageBox
    >     
    >           Prompt
    >
    > Methods
    >
    >       ......
    >
    > MessageBox
    >
    > Description
    >
    > Display a message
    >
    > Remarks
    >
    > The MessageBox function displays a message string to the user. The optional   title string sets the title or caption of the MessageBox. The buttons that    appear on the MessageBox can be configured by passing a combination of numeric     values in the optional ‘buttons’ parameter. By default MessageBox will display  the message string with an OK button. However, many possibilities exist for  displaying message boxes with different icons, and buttons.
    >
    > The MessageBox function returns a numeric value that can be used to identify  which button was clicked.
    >
    > VBScript
    >
    > Syntax
    >
    > ```crt.Dialog.MessageBox(message [, title [, buttons]])```
    >
    > Example
    >
    > The following code sample defines the constants that can be combined to form  the "button" parameter as well as the possible numeric return values:
    >
    > ```result = crt.Dialog.MessageBox("Login Failed, Retry?", "Error",   ICON_QUESTION Or BUTTON_YESNO Or DEFBUTTON2 )```
    >
    > Prompt
    >
    >       ......

    使用方法非常简单：```crt.Dialog.MessageBox("字符串")```

2. 文件发送

    > Description
    >
    >       The FileTransfer object provides methods for performing file transfers  initiated by scripts.
    >
    > Syntax
    >
    > ```crt.FileTransfer.Method([arglist])```
    >
    > ```crt.FileTransfer.Property [ = varname ]```
    >
    > Remarks
    >
    >       SecureCRT's FileTransfer object is accessed through the top-level   object’s FileTransfer property. The FileTransfer object is not supported with     with sessions that use TN3270 emulation.
    >
    > FileTransfer Object Properties and Methods
    >
    >       Methods
    >
    >           SendYmodem
    >
    > SendYmodem
    >
    > Description
    >
    >       Sends specified file.
    >
    > Remarks
    >
    >       SendYmodem sends the specified file using the Ymodem protocol. Note,    the appropriate Ymodem receive command should be sent or initiated on the  remote system before executing SendYmodem for the transfer to begin properly.
    >
    > Errors:
    >
    > 1. If the SendYmodem method is executed while not connected the following     script error is generated:```"FileTransfer.SendYmodem: not connected"```
    > 2. If the file list is empty, the following script error is   generated:```"FileTransfer.SendYmodem: No files were specified for transfer."```
    > 3. If a transfer is already in progress when this is called, the following    script error is generated:```"FileTransfer.SendYmodem: A file transfer cannot  be started while another transfer is in progress"```
    >
    > VBScript
    >
    > Syntax
    >
    > ```crt.FileTransfer.SendYmodem```
    >
    > Example
    >
    > ' upload several files using Ymodem
    >
    > ```crt.FileTransfer.AddToUploadList "c:\temp\File1.txt"```
    >
    > ```crt.FileTransfer.AddToUploadList "c:\temp\File2.txt"```
    >
    > ```crt.FileTransfer.AddToUploadList "c:\temp\File3.txt"```
    >
    > ```crt.FileTransfer.SendYmodem```

    使用说明：

    1. 清空发送列表：```crt.FileTransfer.ClearUploadList```
    2. 添加文件加入发送列表：```crt.FileTransfer.AddToUploadList 文件路径```
    3. 发送文件：```crt.FileTransfer.SendYmodem```

## 脚本实例

    ```vb
    #$language = "VBScript"
    #$interface = "1.0"

    crt.Screen.Synchronous = True

    ' This automatically generated script may need to be
    ' edited in order to work correctly.

    Sub Main
        ` 命令行提示符
        str_propt = "$"
        ` 版本查询指令
        cmd_version = "version"
        ` 升级指令
        cmd_upgrade = "upgrade"
        ` 目标版本反馈字符串
        last_version = "version:1.0"
        ` 文件路径
        file_path = "C:test.bin"
        While True
            crt.Screen.Send chr(13)
            ret = crt.Screen.WaitForString(str_propt, 1)
            If ret <> 0 Then
                crt.Screen.Send cmd_version & chr(13)
                ret = crt.Screen.WaitForString(last_version, 1)
                If ret <> 0 Then
                    crt.Dialog.MessageBox("The firmware is last version")
                Else
                    crt.Screen.Send cmd_upgrade & chr(13)
                    ret = crt.Screen.WaitForString("CCC", 10)
                    If ret <> 0 Then
                        crt.FileTransfer.ClearUploadList
                        crt.FileTransfer.AddToUploadList file_path
                        crt.FileTransfer.SendYmodem
                        ret = crt.Screen.WaitForString(last_version, 20)
                        If ret <> 0 Then
                            crt.Sleep 1000
                        Else
                            crt.Dialog.MessageBox("Upgrade fail ???")
                        End if
                    End if
                End if
            End if
        Wend
    End Sub
    ```
