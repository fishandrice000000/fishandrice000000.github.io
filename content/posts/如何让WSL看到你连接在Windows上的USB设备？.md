---
date: '2025-10-21T14:45:06+08:00'
draft: false
title: '如何将物理连接在Windows上的USB设备挂载到WSL？'
tags: ["Windows", "WSL"]
---

## 1. 在`Windows`中安装`usbipd-win`

`usbipd-win`允许你**通过网络共享USB设备**

也就是说，这个方法可以使物理连接在`Windows`主机下的USB设备，挂载到`WSL2`上来使用

你可以在管理员`Powershell`输入如下指令来安装它

```powershell
winget install --interactive --exact "dorssel.usbipd-win"
```

这之后, 重新打开一个`Powershell`终端，在新的终端里通过这个指令来验证安装是否成功

```powershell
usbipd --version
```

如果输出了版本号等信息，说明安装成功。

---

## 2. 找到你想要共享的USB设备

使用如下命令可以列出`Windows`主机当前连接到的所有USB设备

```powershell
usbipd list
```

其结果可能会像这样

```powershell
Connected:
BUSID  VID:PID    DEVICE                                                     STATE
1-2    10c4:ea60  CP2102 USB to UART Bridge Controller                      Not shared
2-1    24ae:187b  Rapoo Wireless Device Driver, USB 输入设备                 Not shared
2-2    1a2c:8324  USB 输入设备                                               Not shared
4-1    5986:211c  HD Webcam                                                  Not shared
4-2    1038:113a  USB 输入设备                                               Not shared
4-3    0489:e10a  Qualcomm FastConnect 7800 Dual Bluetooth Adapter           Not shared

Persisted:
GUID                                  DEVICE
```

在这里，像`CP2102 USB to UART Bridge Controller`就是我想共享的USB设备

记下它的`BUSID`: `1-2`

如果你无法确定，你可以将那个USB设备拔下后，再次使用`usbipd list`，对比输出结果，消失的那个USB设备就是你目标中的USB设备

---

## 3. 在`Windows`中共享你的USB设备

使用如下命令将对应设备的`STATE`改为`Attached` ，注意将`1-2`替换为你实际的`BUSID`

这可能需要管理员权限，所以请在管理员终端下使用这个命令

```powershell
usbipd bind --busid 1-2
```

---

## 4. 将USB设备挂载到你的`WSL`上

首先使用如下命令获得`WSL`的版本号，记下你想挂载的`WSL`的名字，如`Ubuntu-22.04`

```powershell
wsl -l -v
```

接着将之前记下来的`WSL`名字和`BUSID`使用到如下的命令中

```powershell
usbipd attach --wsl Ubuntu-22.04 --busid 1-2
```

如果你再次使用`usbipd list`时，你想共享的`USB`设备没有显示出来，那么就说明你的操作成功了

---

## 5. 在`WSL`中验证挂载成功

打开你对应的`WSL`终端，使用以下命令

```bash
lsusb
```

此时如果你看见了对应的USB设备，那么就意味着你成功了！
