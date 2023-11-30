---
title: 如何手动破解Typora软件
toc: true
date: 2023-12-26 13:35:00
tags:
categories: tool
---

## 软件介绍
Typora是一款轻量级的Markdown编辑器，适用于OS X、windows、linux三种系统。目前只有15天免费试用，后续使用必须拥有许可才行。

## 软件官方地址

[typora](https://typora.io)

## 破解步骤

1. 修改Typora安装目录\resources\page-dist\static\js\LicenseIndex.xxxxxxxxx.xxxxxxx.chunk.js，激活主程序
```txt
# 查找下列字符串
e.hasActivated="true"==e.hasActivated,
# 替换成下列字符串
e.hasActivated="true"=="true",
```
2. 修改 Typora安装目录\resources\page-dist\license.html，关闭每次启动时的已激活弹窗
```txt
# 查找下列字符串
</body></html>
# 替换成下列字符串
</body><script>window.onload=function(){setTimeout(()=>{window.close();},5);}</script></html>
```
3. 修改 Typora安装目录\resources\locales\zh-Hans.lproj\Panel.json，去除左下角“未激活”提示
```txt
# 查找下列字符串
"UNREGISTERED":"未激活"
# 替换成下列字符串
"UNREGISTERED":" "
```

注意： 1.76版本能破解成功


## 参考资料
> - []()
> - []()
