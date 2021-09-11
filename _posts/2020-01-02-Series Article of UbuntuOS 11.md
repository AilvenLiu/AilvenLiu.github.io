---
layout:     post
title:      Series Article of UbuntuOS -- 11       
subtitle:   查看系统当前登录用户信息的方法               
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

> 进行非紧急 GPU 计算的时候，为防止影响他人工作，有意监测账户登录信息，一旦检测到特定或其他账户登录，则退出本账户当前工作。当然最后由于种种原因没有实现。       

转载自 [4 Ways to Identify Who is Logged-In on Your Linux System](https://www.thegeekstuff.com/2009/03/4-ways-to-identify-who-is-logged-in-on-your-linux-system/)。     

作为系统管理员，你可能经常会（在某个时候）需要查看系统中有哪些用户正在活动。有些时候，你甚至需要知道他（她）们正在做什么。本文为我们总结了4种查看系统用户信息（通过编号（ID））的方法。      

## 使用w命令查看登录用户正在使用的进程信息       
`w` 命令用于显示已经登录系统的用户的名称，以及他们正在做的事。该命令所使用的信息来源于 `/var/run/utmp` 文件。w命令输出的信息包括：       
* 用户名称         
* 用户的机器名称或tty号       
* 远程主机地址      
* 用户登录系统的时间      
* 空闲时间（作用不大）      
* 附加到tty（终端）的进程所用的时间（JCPU时间）     
* 当前进程所用时间（PCPU时间）     
* 用户当前正在使用的命令      

w命令还可以使用以下选项：      
`-h` 忽略头文件信息      
`-u` 显示结果的加载时间     
`-s` 不显示JCPU， PCPU， 登录时间       

```bash     
ailven@haida-KI4208G:~$ w
15:41:15 up 99 days, 17:19, 14 users,  load average: 0.81, 0.88, 0.86
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
ailven   pts/0    10.118.105.102   15:41    2.00s  0.04s  0.00s w
qiqi     pts/1    tmux(11899).%4   06Jul21  8days  0.32s  0.32s -bash
ailven@haida-KI4208G:~$ 
ailven@haida-KI4208G:~$ w -h
ailven   pts/0    10.118.105.102   15:41    0.00s  0.04s  0.00s w -h
qiqi     pts/1    tmux(11899).%4   06Jul21  8days  0.32s  0.32s -bash
ailven@haida-KI4208G:~$
ailven@haida-KI4208G:~$ w -u
15:41:39 up 99 days, 17:20, 14 users,  load average: 0.91, 0.90, 0.87
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
ailven   pts/0    10.118.105.102   15:41    3.00s  0.05s  0.01s w -u
qiqi     pts/1    tmux(11899).%4   06Jul21  8days  0.32s  0.32s -bash    
ailven@haida-KI4208G:~$
ailven@haida-KI4208G:~$ w -s
 15:41:50 up 99 days, 17:20, 14 users,  load average: 0.77, 0.87, 0.86
USER     TTY      FROM              IDLE WHAT
ailven   pts/0    10.118.105.102    6.00s w -s
qiqi     pts/1    tmux(11899).%4    8days -bash
```      

## 使用who命令查看（登录）用户名称及所启动的进程        
who命令用于列举出当前已登录系统的用户名称。其输出为：用户名、tty号、时间日期、主机地址。如果只希望列出用户，可以使用管道操作符。             
```bash       
ailven@haida-KI4208G:~$ who
ailven   pts/0        2021-09-11 15:41 (10.118.105.102)
qiqi     pts/1        2021-07-06 15:21 (tmux(11899).%4)
ailven@haida-KI4208G:~$
ailven@haida-KI4208G:~$ who | cut -d' ' -f1 | sort | uniq
ailven
qiqi
```     

## last 随时查看系统的历史信息      
last命令可用于显示特定用户登录系统的历史记录。如果没有指定任何参数，则显示所有用户的历史信息。在默认情况下，这些信息（所显示的信息）将来源于 `/var/log/wtmp` 文件。该命令的输出结果包含以下几列信息：
* 用户名称      
* tty设备号     
* 历史登录时间日期     
* 登出时间日期     
* 总工作时间       

```bash      
ailven@haida-KI4208G:~$ last ailven
ailven   pts/0        10.118.105.102   Sat Sep 11 15:41   still logged in
ailven   pts/15       10.118.105.102   Thu Sep  9 14:35 - 16:50  (02:15)
ailven   pts/14       10.118.105.102   Wed Sep  8 15:39 - 15:51  (00:12)
ailven   pts/14       10.118.105.102   Wed Sep  8 15:29 - 15:38  (00:09)
ailven   pts/14       10.118.105.102   Wed Sep  8 15:20 - 15:26  (00:06)

wtmp begins Wed Sep  1 09:55:25 2021
```      