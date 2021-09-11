---
layout:     post
title:      Series Article of UbuntuOS -- 10       
subtitle:   用户增删查看及 sudo 权限赋予               
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

**添加用户：**       
```bash       
$ sudo user add -r -m -s /bin/bash username      
$ sudo passwd username
```     
其中参数：     
`-r`: 建立系统账号，可以检测到其登录信息；      
`-m`: 自动建立路径为 `/home/username` 的登录家路径；      
`-s`: 指定登录时的 shell，建议指定为 `/bin/bash`，好用；       

建立用户之后需要 `passwd` 赋予密码才可以登录。       

**删除用户：**      
```bash     
$ sudo userdel username       
```      

**赋予/删除 sudo 权限：**       
本质也就是是否加入 sudoer 组。编辑 `/etc/sudoers` 文件，在复制一行 `xxx ALL=() ALL` 内容，将 xxx 改为需要给予 sudo 的用户名即可。删除同理。     
`/etc/sudoers` 文件需要 sudo 访问，且，务必不要改变他的权限，如果保存失败，使用 `wq!` 保存。      
如果不小心改变了权限，sudo 将无法使用。解决方案可见 [修改/etc/sudoers权限导致无法使用sudo的问题]()

**查看本系统所有用户：**       
本系统所有赋予过密码的用户都保存在 `/etc/passwd` 文件中，则直接 cat 查看即可，一般最后几行 id 比较大的是用户创建的新用户。       