---
layout:     post
title:      Series Article of UbuntuOS -- 31         
subtitle:   根目录忽然空间不足问题解决记录    
date:       2022-04-26      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---

问题已解决，出在系统日志上。现在简单复盘记录一下发现和处理流程。        
先 `df -h` 查看一下那个分区占用大，果不其然是 根目录，则进入到根目录执行 `sudo du -h --max-depth=1` ，发现 `./var` 占用了超大空间。进入，继续 `sudo du -h --max-depth=1` ，发现 `./log` 占用了超大空间。进入，执行 `sudo du -sh * | grep G` 发现 `journal`, `syslog`, `syslog.1` 占用了超大空间，不能直接删，直接删会出问题的。执行下面操作清空日志存储：           
```bash
sudo su
cat /dev/null > /var/log/syslog.1
cat /dev/null > /var/log/syslog
cat /dev/null > /var/log/journal
```

得救。 