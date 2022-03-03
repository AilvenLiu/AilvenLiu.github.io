---
layout:     post
title:      Life Recording -- 07 
subtitle:   vscode重复输入密码操作的解决方案     
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space      
---

Vscode远程登录服务器出现重复要求输入密码的现象。可能的原因：**因为之前某次异常退出**。      
解决方案：**Kill VS Code Server on Host**。            

左下角设置 --> 命令面板（Command palette）--> 在命令面板中输入 Remote SSH，然后找到 Kill VS Code Server on Host：          

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/vscode01.png"></div>      

然后选择对应的服务器地确认即可。          

不仅仅是重复输入密码问题，可能很多问题都可以依靠这个方案去解决