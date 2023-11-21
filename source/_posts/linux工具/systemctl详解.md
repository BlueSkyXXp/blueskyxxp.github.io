---
title: systemctl详解
toc: true
date: 2023-11-21 23:33:57
tags:
categories:
---


## 简介
systemctl是一个systemd工具，负责控制systemd系统和服务管理。
## 工具的使用
systemd作为父守护进程运行（PID = 1）
```shell
# 查看systemd进程信息
ps -eaf | grep systemd
# 重启系统
systemctl reboot
# 关闭系统，切断电源
systemctl poweroff
# cpu停止工作
systemctl halt
# 暂停系统
systemctl suspend


# 查看系统启动耗时
systemd-analyze
#-------
# output is :
#Startup finished in 2.900s (kernel) + 12.773s (userspace) = 15.674s
#graphical.target reached after 12.761s in userspace
#-------
# 查看每个服务的启动耗时
systemd-analyze blame
#-------
# output is :
#         20.138s healthd2.service
#          7.812s casaos-local-storage.service
#          3.908s casaos-app-management.service
#          3.005s networking.service
#          1.251s user@1000.service
#          1.217s user@0.service
#          1.213s user@2000.service
#          1.105s casaos-user-service.service
#          1.095s nfs-server.service
#           984ms casaos-message-bus.service
#           791ms casaos-gateway.service
#           775ms ifupdown-pre.service
#           738ms casaos.service
#           614ms apt-daily-upgrade.service
#           565ms apt-daily.service
#           493ms cloud-init-local.service
#           423ms docker.service
#                 .....                     
#-------

# 查看启动的关键链接
systemd-analyze  critical-chain
#------
#output is :
#graphical.target @12.761s
#└─multi-user.target @12.760s
#  └─exim4.service @6.250s +350ms
#    └─network-online.target @6.243s
#      └─network.target @3.927s
#        └─ifupdown-pre.service @463ms +775ms
#          └─systemd-udev-trigger.service @370ms +89ms
#            └─systemd-udevd-kernel.socket @368ms
#              └─system.slice @332ms
#                └─-.slice @332ms
#-------

```



## 参考资料
> - [systemctl](https://www.jianshu.com/p/3dd6b57a16bf)
> - [systemd服务配置文件详解](https://blog.csdn.net/qq_28505809/article/details/132868792)
> - [centos6.X中没有systemd](http://pythontime.iswbm.com/en/latest/c08/c08_06.html#id17)