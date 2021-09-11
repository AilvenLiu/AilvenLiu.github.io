---
layout:     post
title:      Series Article of UbuntuOS -- 14       
subtitle:   修改/etc/sudoers权限导致无法使用sudo的问题               
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

由于一般人蠢不到改变 sudoers 的地步，所以没有在网络上找到相关问题及解决方案。于是，本问题及其解决方案在中文网络世界首次出现可见本人在 ubuntu 中文社区的提问 [修改/etc/sudoers权限导致的无法使用sudo问题](https://forum.ubuntu.org.cn/viewtopic.php?t=492090) 。      

#1    
帖子 由 OUC_LiuX » 2021-04-23 17:55    
错误地修改/etc/sudoers权限为777，之后使用sudo命令报错：     
sudo:sudo /etc/sudoers is world writable      
sudo:no valid sudoers sources found ,quitting      
sudo:unable to initialize policy plugin     

系统为ubuntu1804Server，请求论坛支持。     

#2    
帖子 由 astolia » 2021-04-23 19:47     
如果你以前设置过root的密码，用su输入root密码获取root shell      
如果没有，重启在grub菜单处，选高级选项，进入恢复模式，在恢复菜单中选root，用root登录。要密码时直接回车就行      

#3    
帖子 由 OUC_LiuX » 2021-04-24 16:14       
首先感谢您的回复！      
我第一时间想到了这个解决办法，可问题在于没有办法进入grub界面。现在服务器开机直接进入系统，不经过grub引导。     
我知道的进入grub引导的方法是修改grub文件，又由于修改grub文件需要sudo权限，所以，没有办法了。     

#4     
帖子 由 onlylove » 2021-04-24 16:53     
那就用livecd启动机器，然后挂载硬盘改吧       

#6     
livecd进入系统挂载硬盘后      
代码： 全选      
```bash    
$ sudo chmod 0440 /etc/sudoers
```       

问题解决。     
很奇怪的是上述操作反复进行到第三次才奏效。前两次执行上述操作，在 livecd 系统中已经 `ls -hl /etc/sudoers` 检查过该文件权限状态为 `r--r-----` ，但是进入服务器系统却没有改变，直到第三次执行上述操作在进入系统，才恢复。     
感谢 **@astolia** 和 **@onlylove** 提供的帮助！      