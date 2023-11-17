---
title: linux系统开机启动流程
date: 2023-11-17 15:25:00
tags:
---


## [linux系统开机启动流程](https://blog.csdn.net/qq_51010919/article/details/131465536)

### 开机详细流程概览

1. power on 开机
2. POST 开机自检
3. BIOS/UEFI 硬件检查
4. Boot 检查启动顺序
5. hard disk 硬盘
6. MBR 引导分区 或者GPT分区
7. grub2 引导程序
8. 加载/boot中ext4文件系统驱动
9. load kernel 加载内核文件vmlinuz initramfs到内存
10. systemd 启动第一个进程
11. 启动对应运行级别服务
12. mutil-user /etc/rc.local  /etc/fstab开机自动挂载file system
13. login 登入
14. 根据/etc/passwd和/etc/shadow检测用户名和密码是否正确
15. 初始化用户级别环境变量 ~/.bashrc ~/.bash_profile /etc/bashrc /etc/profile
16. bash提示

### 步骤解释

1. POST(power-on Self-Test) 开机自检
    主板是所有硬件的载体，声卡、显卡、网卡所有的硬件之间的传输都靠主板传输数据。主板中有个BIOS程序会完成自检工作
    主要检测 cpu、内存、显示器适配器、键盘、鼠标、硬盘、可启动设备、引导加载程序bootloader初始化
    一旦完成自检后，会把程序交给引导加载程序
2. BIOS是固化在主板上ROM芯片上的程序
    如何进入BIOS系统
    + 台式机： 开机按 delete键
    + 服务器： 开机有提醒，按照提醒操作
    + 笔记本： F1 ～ F12等
    
    UEFI： UEFI的出现是为了取代传统的BIOS
3. Boot引导顺序，一般BIOS会提供多种引导系统的顺序
    + hard drive 硬盘， 通过硬盘去启动引导程序
    + cd-raw 光驱，通过光驱去启动引导程序
    + removable device 可移动设备
    + network 从网络中启动

4. MBR/GPT 主引导分区快 内部包含引导加载程序，
    MBR缺点不支持容量大于2T的硬盘
    GPT最大支持18EB的硬盘，是基于UEFI使用的磁盘分区架构

    目前BIOS只支持MBR引导系统，UEFI可以支持GPT和MBR

    linux可以使用以下命令查看是否是MBR分区还是GPT分区
    ```shell
    parted -l
    ```
    ![parted -l](/img/article/cloud-compute/system-boot-bios-mbr.png)

    partition table如何输出GPT则表示是GPT分区；如果输出是msdos则表示MBR
    [腾讯制作linux镜像](https://cloud.tencent.com/document/product/213/17814)

5. grub2 引导装载程序 ，会负责选择要启动的操作系统、加载内核文件并传递参数
    + 提供启动菜单：grub2在计算机启动时会列出可用的操作系统和内核选项
    + 加载系统内核：加载内核文件（eg：vmlinuz）到内存中
    + 加载初始RAM磁盘映像initramfs：initramfs中包含引导系统的必要文件和驱动程序
    + 启动操作系统：当内核和initramfs加载后，会在内存中形成一个临时的根文件系统，grub2将机器控制权交给内核，然后进行操作系统的初始化
    + 提供启动参数：grub2可以设置内核参数、运行级别等
    + 提供多重引导：用户可以轻松切换不同操作系统。

6. systemd 是一种初始化系统的程序，负责引导过程中启动各个服务和进程，并管理系统的运行状态。
    其作用包含以下几个方面：
    + 引导管理：硬件初始化、文件系统挂载、设备驱动加载
    + 服务管理： 各个服务的启动停止状态监控等
    + 并行启动：支持并行启动互不依赖的服务，加快系统启动速度
    + 日志管理：journald日志系统
    + 用户会话管理：

7. 

