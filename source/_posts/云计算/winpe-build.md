---
title: 制作WinPE
date: 2023-11-14 15:55:42
categories:
- 镜像
tags:
- 镜像
excerpt: Winpe镜像的制作。
---
## 1. 简介 introdution

WinPE 本身是一个最小的操作系统，有 2 个主要的用途：
+ 在修复故障 Windows 的时候充当中间介质角色。
+ 用来部署和安装操作系统。

WinPE 属于 RAM Disk，在 WinPE 启动后，对 WinPE 其中内容进行的改动会在下一次重启时还原。更多信息可以参考：[Windows PE英文版](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/winpe-intro?view=windows-10)  [Windows PE中文版](https://learn.microsoft.com/zh-cn/windows-hardware/manufacture/desktop/winpe-intro?view=windows-10)

## 2. 安装前的准备 Preparation before installation
### 2.1 选择相应版本的ADK
WinPE 的制作基于 Windows ADK 中的部署工具以及附带 WinPE 的文件。需要注意在 Windows 10, 1809 之前的 ADK 版本，ADK 和 WinPE 是集合在一个安装包中的。而较新的 ADK 版本则将 ADK 与 WinPE add-on for ADK 分开为 2 个不同的安装包。
在作为修复故障虚拟机的用途时，不同版本的之间的 WinPE 没有太大的区别，新版本的 WinPE 会多一些命令行工具。如果环境中有多个版本，推荐选择环境中最新版本的 Windows所对应的 ADK 来制作 WinPE。

更多信息的可以参考：[英文版](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install#choose-the-right-adk-for-your-scenario)  [中文版](https://learn.microsoft.com/zh-cn/windows-hardware/get-started/adk-install#choose-the-right-adk-for-your-scenario)

### 2.2 安装ADK
1. 下载 [Windows ADK 工具以及 WinPE add-on for ADK](https://learn.microsoft.com/zh-cn/windows-hardware/get-started/adk-install) 

分别点击以下 2 个链接进行下载 Windows Server 2022 版本的 ADK 以及 WinPE add-on for ADK
分别安装，先 ADK (仅勾选部署工具即可)，后 Windows PE
![下载win adk](/img/article/image/image-download-win-adk.png)
![安装win adk](/img/article/image/install-win-adk.png)
![安装win adk](/img/article/image/install-win-adk-2.png)

## 3. WinPE制作步骤
1. 以管理员身份运行部署和映像工具环境
   ![管理员运行](/img/article/image/image-run-adk-cmd.png)
2. 在打开的窗口运行以下的命令，将 64 位的 PE 环境拷贝到 C:\WinPE_amd64
    ```powershell
    copype amd64 C:\WinPE_amd64
    ```
3. 在打开的部署和映像工具环境 CMD 窗口中通过以下的命令直接封装 ISO 文件：
    ```powershell
    MakeWinPEMedia /ISO C:\WinPE_amd64 C:\WinPE_amd64.iso
    ```
    ![将ios文件挂在在某个目录](/img/article/image/winpe-mount-iso.png)
    
    如果需要封装成 VHDX，可以用以下的 CMD 命令 (管理员权限) 先创建一个 VHDX：
    ```powershell
    diskpart
    create vdisk file="C:\WinPE.vhdx" maximum=1000
    attach vdisk
    create partition primary
    assign letter=V
    format fs=ntfs quick
    exit
    ```
    ![创建虚拟盘](/img/article/image/winpe-create-vhdx.png)

    然后在部署和映像工具环境 CMD 窗口中通过以下的命令将 WinPE 制作为 VHD：
    ```powershell
    MakeWinPEMedia /UFD C:\WinPE_amd64 V:
    ```
    ![制作whd](/img/article/image/winpe-create-vhd-success.png)

    最终，将 V 盘弹出：
    ![弹出盘](/img/article/image/winpe-vhd-export.png)
   
## 4. WinPE 离线注入驱动文件
由于环境中的 Windows 是基于 KVM 的虚拟机，所以需要考虑为 WinPE 离线注入对应的驱动。最重要的是磁盘控制器的驱动，这是磁盘正常加载的前提，也是对问题机器修复的前提。其次需要考虑的是网卡驱动，网卡驱动可以使得 WinPE 网络联通，以访问一些共享路径进行文件拷贝动作。
因为离线安装驱动的 WinPE 内核版本是基于 Windows Server 2022，所以只需要为该版本的WinPE 导入 Windows Server 2022 版本的磁盘控制器驱动以及网卡驱动。
参考步骤如下：
在我的电脑资源管理器中，打开以下的目录 C:\WinPE_amd64\media\sources，该目录下会有boot.wim (WinPE 的本体)。将其 mount 起来，使用 DISM 离线打入驱动，然后再 unmount /commit 提交变更，具体步骤如下：

1. 利用管理员权限新打开一个 CMD 窗口，
    利用以下的命令将 C:\WinPE_amd64\media\sources\boot.wim挂载到 C:\WinPE_amd64\mount这个文件夹中
    ```powershell
    dism.exe /mount-image /imagefile:"C:\WinPE_amd64\media\sources\boot.wim" /index:1 /mountdir:"C:\WinPE_amd64\mount"
    ```
    ![管理员打开cmd](/img/article/image/winpe-open-cmd.png)

    观察挂载后的目录，和平常操作系统的 C 盘目录结构相似：
    ![系统盘目录](/img/article/image/winpe-c.png)
2. 通过 DISM 命令，离线安装驱动
    ```powershell
    dism.exe /Image:"C:\WinPE_amd64\mount" /Add-Driver /Driver:"C:\drivers\mydriver.inf"
    ```
    ![离线安装驱动](/img/article/image/winpe-offline-install-driver.png)

    安装完成后，可以使用以下的命令查看安装的驱动：
    ```powershell
    dism.exe /Image:"C:\WinPE_amd64\mount" /Get-Drivers
    ```
    ![查看驱动](/img/article/image/winpe-describe-driver.png)

3.  提交变更，并 unmount-image(建议操作前关闭所有的文件夹窗口，以避免报错文件被占用)
    ```powershell
    dism.exe /unmount-image /mountdir:"C:\WinPE_amd64\mount" /commit
    ```
    ![卸载挂载](/img/article/image/winpe-umount-image.png)

    ::: warning
    注：如果提示报错 0xc1420117，请关闭所有与 mount 路径相关的窗口以及应用，并重新运行上述命令，若报错变更为 0xc142011d，请运行以下的命令：
    ```powershell
    dism.exe /unmount-image /mountdir:"C:\WinPE_amd64\mount" /discard
    ```
    :::
    参考 3.3 中的后面的 3 步骤利用 MakeWinPEMedia 将 WinPE 制作成 ISO 或者 VHDX。

## 5. WinPE 引导验证

根据以往的经验，某些高版本内核的 Windows 启动是需要一定的固件版本基础的 (在 VMware 平台上遇到过 Windows Server 2019 无法在低版本的固件上启动的案例的)，换一句话说，在该 WinPE 制作完成后，需要测试其能在不同版本 Windows Server 所在的固件平台上正确引导。引导进入 WinPE 后，需要确认能否识别 disk，网卡等相关信息。

+ 查看磁盘状态，查询完成后exit退出
    ```powershell
    diskpart 
    list disk 
    list volume
    exit
    ```

+ 查看网卡状态

    ```powershell
    Ipconfig
    ```
+ 查看设备状态

    ```powershell
    pnputil.exe /enum-devices 
    ```
+ 查看驱动状态

    ```powershell
    pnputil.exe /enum-drivers 
    ```

使用上述的命令查看信息时，可以将信息导出为 txt 文件，然后使用 notepad 打开查看或者是拷贝到其他地方进行查看，如下示例
ds1
```powershell
pnputil.exe /enum-devices > X:\Windows\System32\devicesinfo.txt 
notepad.exe X:\Windows\System32\devicesinfo.txt 
```

![保存信息](/img/article/image/winpe-open-notepad.png)

## 6. 原pdf文件
<div>
  <iframe src="/pdfjs/web/viewer.html?file=data/WinPE.pdf" width="100%" height="500px" frameborder="0"></iframe>
</div> 