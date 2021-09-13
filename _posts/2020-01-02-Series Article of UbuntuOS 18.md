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
1. 每分钟执行一次       
   `* * * * * myCommand`       
2. 每小时的第3和第15分钟执行       
   `3,15 * * * * myCommand`       
3. 在上午8点到11点的第3和第15分钟执行        
   `3,15 8-11 * * * myCommand`       
4. 每隔两天的上午8点到11点的第3和第15分钟执行        
   `3,15 8-11 */2  *  * myCommand`       
5. 每周一上午8点到11点的第3和第15分钟执行      
   `3,15 8-11 * * 1 myCommand`         
6. 每晚的21:30重启smb       
   `30 21 * * * /etc/init.d/smb restart`       
7. 每月1、10、22日的4 : 45重启smb       
   `45 4 1,10，22 * * /etc/init.d/smb restart`        
8. 每周六、周日的1 : 10重启smb      
   `10 1 * * 6,0 /etc/init.d/smb restart`       
9. 每天18 : 00至23 : 00之间每隔30分钟重启smb      
    `0,30 18-23 * * * /etc/init.d/smb restart`       
10. 每星期六的晚上11 : 00 pm重启smb       
    `0 23 * * 6 /etc/init.d/smb restart`      
11. 每小时重启一次smb      
    `0 */1 * * * /etc/init.d/smb restart`        
12. 晚上11点到早上7点之间，每隔一小时重启smb      
    `0 23-7/1 * * * /etc/init.d/smb restart`       

## 根据进程关键词杀死任务     
由于进程需要定时启动，每次任务的 pid 都不一样，于是要做到自动定时杀死进程，需要先行自动获取进程 pid 。       
假如我们的任务是 `ipython` 它有唯一（指和其他任何进程相比的 unique ）关键词 `ipython` :    
```shell   
$ ps -ef | grep ipython | grep bin | awk '{print $2}'|xargs kill -9
```       
这句话的意思是：       
* `ps -ef | grep ipython `： 返回进程名包含 ipython 字段的进程信息。     
* `| grep bin`： 使用管道接受上一步的返回结果，并再此筛选上一步返回结果中包含 bin 的进程信息；这是为了排除其他比如 color=auto 这样的干扰信息，因为 ipython 这个 **进程** 的路径包含 bin，而其他则不一定；于是使用一个 bin 关键词进行二次确定。      
* `| awk  '{print  $2}'`： 使用管道接受上一步的返回信息，提取返回信息的第二列，即进程 pid 。     
* `| xargs kill -9`： 使用管道接受上一步的返回信息（PID），作为 xargs，并杀死 pid 为 xargs 的进程。这是一个固定格式。       
  
## 定时启动杀死进程       
有了上述信息，即可做到定时启动杀死进程。加入我们要进行定时的任务是 `bminer_start.sh`，其中可执行文件的绝对路径为 `/home/OUC_LiuX/unique/bminer.sh`，需要每天 11:30 pm 启动，次日 6:30 am 停止，任务裸奔不需要独立环境：      
使用 `crontab -e` 编辑定时任务表，添加以下两行：      
```       
30 23 * * * bash /home/OUC_LiuX/unique/bminer_start.sh         
30 6 * * * ps -ef | grep bminer_start.sh | grep unique | awk '{print $2}' | xargs kill -9        
```          

完美结束。       
