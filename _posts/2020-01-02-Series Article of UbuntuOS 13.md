---
layout:     post
title:      Series Article of UbuntuOS -- 13       
subtitle:   无法获得锁/var/lib/dpkg/lock-frontend-open(11:资源暂时不可用)               
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

## 错误出现      
使用 apt-get 安装程序时可能出现下面的报错。      
E: 无法获得锁 /var/lib/dpkg/lock-frontend - open (11: 资源暂时不可用)      
E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), is another process using it?    

## 错误原因     
这个报错是因为有另外一个程序正在运行，由于它在运行时，会占用软件源更新时的系统锁（在“/var/lib/apt/lists/”目录下），而当有新的apt-get进程生成时，就会因为得不到锁而出现"E: 无法获得锁 /var/lib/apt/lists/lock - open (11: 资源暂时不可用)"错误提示！     
而导致资源被锁的原因，可能是上次安装时没正常完成，而导致出现此状况。    
所以，我们只需要关闭掉之前的安装操作，将进程释放，解开锁就可以啦。     

## 解决方案      
两种方案如下：     
1. kill 掉进程。       
   `ps -ef | grep apt` 打印出相关进程，`kill` 全部杀掉。     
2. 强制释放锁。       
   ```bash      
   $ sudo rm /var/cache/apt/archives/lock      
   $ sudo rm /var/lib/dpkg/lock      
   ```     
实践证明，第二种方法有用。如果强制释放锁后仍然出现报错，但是查看进程发现并没有apt-get进程信息，重启吧，重启就行了。    
