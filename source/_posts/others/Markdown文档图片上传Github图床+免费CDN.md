---
title: Markdown文档图片上传github图床+免费CDN
toc: true
date: 2024-01-23 16:35:51
tags:
categories:
---

# Markdown文档图片上传Github图床+免费CDN

## 背景
在写博客时，常见的一种文档格式是Markdown格式，但是Markdown文档格式有个痛点，对于图片不方便管理。如果在Markdown中使用本地图片路径，分享到各大平台就非常不方便，需要将文章中的图片一并发送，如果能将图片上传到某个云存储中，通过网络路径来访问就能解决这个问题。还有一个问题就是云存储大部分是需要收费的，于是我想到了开源代码仓库。gitee和github仓库是免费开源的，gitee是国内的，在国内访问不受限但是很容易被封，基于以上原因最终选择了国外的github仓库和免费的jsdelivr CDN来实现图床。
## 使用步骤
1. 在github创建图床仓库，需要确保仓库是public。
   ![](https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource@latest/blog/images/create-repo-for-picgo.png)


2. 在github创建token，需要授权仓库权限，用来picGo访问你的仓库。
   进入setting--->Developer settings
   ![](https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource@latest/blog/images/create-github-token-for-picgo.png)
   
3. [下载并安装picgo](https://picgo.github.io/PicGo-Doc/zh/guide/#%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85)
    **注意：macos打开PicGo可能会出现“已损坏，无法打开”等提示。可以使用如下命令解决**
    ```shell
    sudo xattr -r -d com.apple.quarantine '/Applications/PictGo.app'
    ```
4. 配置picgo
    ![](https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource@latest/blog/images/picgo-config.png)
    注意以下几点：
    1.  分支名。
    2.  存储路径就是你仓库中的路径。
    3.  自定义域名推荐 https://cdn.jsdeliv.net/gh/{github用户名}/{仓库名}@latest

5. 配置jsdelivr加速
    jsDelivr加速平台是面对全球开源免费的CDN，可以存放js,css,图片,重要的是jsDelivr在中国大陆也拥有超过数百个节点，因为jsDelivr拥有正规的ICP备案。
    
    访问图片路径方式有两种
   1. 直接访问(不推荐)
        ```txt
        jsDelivr 文件路径: https://cdn.jsdelivr.net/gh/user/repo/file
        例如: https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource/blog/images/picgo-config.png
        ```
        不推荐，不太清楚如何更新cdn缓存。
   2. 每次访问最新文件(推荐)
        ```txt
        jsDelivr 文件路径: https://cdn.jsdelivr.net/gh/user/repo@latest/file
        例如: [https://](https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource@latest/blog/images/picgo-config.png)
        ```
6. 上传后可以去github查看是否上传
    ![](https://cdn.jsdelivr.net/gh/BlueSkyXXp/all-static-resource@latest/blog/images/upload-image-to-github-by-picgo.png)




## 参考资料
> - []()
> - []()
