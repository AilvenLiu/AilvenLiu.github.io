---
layout:     post
title:      Series Article of UbuntuOS -- 18         
subtitle:   crontab 定时启动和杀死任务           
date:       2021-09-12      
author:     OUC_LiuX     
header-img: img/wallpic02.jpg     
catalog: true
tags:
    - Ubuntu OS
---
还有其他的小朋友在做些见不得人的东西。7月亓琦博士告知李老师有人在中心服务器上跑定时任务，我啄么着七月时候币价不高啊，挖什么挖。         
于是也想了解一下定时任务，这样每天深夜定时启动，早上定时结束，只要不是亓琦博士这样的人，还挺安全的。      

## crontab 定时任务     
`crontab` 命令是 cron table 的简写，它是 cron 的配置文件，也可以叫它作业列表，我们可以在以下文件夹内找到相关配置文件。        

* `/var/spool/cron/` 目录下存放的是每个用户包括 root 的 crontab 任务，每个任务以创建者的名字命名。     
* `/etc/crontab` 这个文件负责调度各种管理和维护任务。     
* `/etc/cron.d/` 这个目录用来存放任何要执行的crontab文件或脚本。    
  
我们还可以把脚本放在 `/etc/cron.hourly`、`/etc/cron.daily`、`/etc/cron.weekly`、`/etc/cron.monthly`目录中，让它每 小时/天/星期/月 执行一次。    

### 使用      
```shell     
crontab [-u username]　　　　# 省略用户表表示操作当前用户的crontab     

    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作表)     
```      
`crontab -e` 进入当前用户的工作表编辑，首次进入要选择默认编辑器，有 vim 的话最好选 vim 。内部每行是一条命令。      

`crontab` 的命令构成为 时间 + 动作，其时间有分、时、日、月、周五种，操作符有

* `*` 取值范围内的所有数字       
* `/` 每过多少个数字     
* `-` 从X到Z      
* `,` 散列数字     

### 实例      
1. 每1分钟执行一次 myCommand       
2.  