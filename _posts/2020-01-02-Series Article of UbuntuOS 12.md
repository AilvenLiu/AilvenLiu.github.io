---
layout:     post
title:      Series Article of UbuntuOS -- 12       
subtitle:   查看指定用户所有历史操作               
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

> 有的小朋友啊，总喜欢搞一些莫名其妙的操作。但你的所有操作 linux 都会为你忠实的保存下来。               

进入到指定用户的家目录 `/home/user` 下，用户最后一个退出的 shell 和之前的所有 shell 中的操作记录都保存在隐藏文件 `.bash_history` 中，只需要使用 sudo 查看该文件就行了。     