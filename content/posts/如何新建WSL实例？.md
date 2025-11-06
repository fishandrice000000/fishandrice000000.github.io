---
date: '2025-11-06T13:01:05+08:00'
draft: false
title: '如何新建WSL实例？'
tags: ["Windows", "WSL"]
---
## 1. 准备一个包含Linux发行版文件系统的`.tar`文件

根据需求不同，获得`.tar`文件的方式有两种

### Plan A 你想复制一个现有的`WSL`的实例

你需要使用这个命令来获得相应WSL的备份`.tar`文件
```powershell
# <wsl_name> 是你想备份的WSL的名字，你可以通过命令wsl -l来得到
# <path_to_backup> 是备份文件的保存路径
wsl --export <wsl_name> <path_to_backup>
```

### Plan B 你想获得一个全新的、干净的实例

这种情况你需要去网络上下载相应的镜像

这里以`Ubuntu`为例，你可以点击[这里]([Ubuntu WSL Images](https://cloud-images.ubuntu.com/wsl/) "Ubuntu WSL Images")来获取到`Ubuntu`各个版本的`WSL`镜像

选择一个适合你环境的版本，然后下载即可，文件名可能类似`ubuntu-jammy-wsl-arm64-ubuntu22.04lts.rootfs.tar.gz`，确认一下不要下错

---

## 2. 导入你的新实例

使用这个命令来将新实例导入，可能需要管理员权限，所以确保万无一失请在管理员终端下运行

```powershell
# <new_wsl_name> 是你为这个新WSL实例起的名字
# <path_to_install> 是你的安装路径
# <path_to_.tar> 是你的.tar文件的路径
wsl --import <new_wsl_name> <path_to_install> <path_to_.tar> --version 2
```

这之后打开一个新终端，在其中使用
```powershell
wsl -l
```

检查一下能否找到你新安装的`WSL`，如果找到了，就证明你安装成功了

---

## 3. 进行一些必要的初始化

在这之后在`PowerShell`中使用这个命令进入你新建的`WSL`的终端

```powershell
# <new_wsl_name> 是你为这个新WSL实例起的名字
wsl -d <new_wsl_name>
```

在新建的`WSL`的终端中使用这个命令新建一个用户

```bash
# <new_username> 是你自定义的用户名
adduser <new_username>
```

然后跟着`bash`的提示设定好密码等信息

之后使用这些命令，设定默认登录用户

```bash
echo "[user]" >> /etc/wsl.conf
# <new_username> 是你自定义的用户名
echo "default = <new_username>" >> /etc/wsl.conf
```

接着将新创建的用户添加到`sudo`组

```bash
# <new_username> 是你自定义的用户名
usermod -aG sudo <new_username>
```

然后关闭当前的`WSL`，在`Windows`中打开一个终端，在这个终端中使用这个命令来将那个`WSL`关闭

```powershell
# <new_wsl_name> 是你为这个新WSL实例起的名字
wsl -t <new_wsl_name>
```

之后再次使用`wsl -d <new_wsl_name>`启动

如果进入时用户名显示的是你刚才自定义的名字，那么就说明一切已经设定好了！