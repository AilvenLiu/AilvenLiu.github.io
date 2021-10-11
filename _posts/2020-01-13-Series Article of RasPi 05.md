---
layout:     post
title:      Series Article of RasPi -- 05
subtitle:   树莓派使用实录05 -- 超频，电压频率温度检测，压力测试         
date:       2021-10-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - RasPi   
---     

以树莓派4B为例子：

### 超频           
编辑 `/boot/config.txt` 文件，修改如下内容：
```
force_turbo=0
arm_freq=2000
over_voltage=10
```            

不要试图设置 `force_turbo = 1`，因为此选项会允许进一步提升电压，它会改变芯片中的保险结构，这将使您的保修失效。我们现在可以重新启动主板以验证设置，并检查设置内容是否被应用：           
```shell       
$ vcgencmd get_config int | grep "arm\|over"
arm_freq=2000
over_voltage=10
over_voltage_avs=-23750
```       

不设置 `force_turbo` 的情况下，`over_voltage` 个人测试达到 10 是 1.1109V ，再高的值电压也不再增加。
`over_voltage=6` 时超频到2G日常使用中偶见电压不足，甚至重启的情况，所以这里设置为10。树莓派4 over_voltage与电压对照表如下：            
```        
0     0.8630V
1     0.8859V
2     0.9158V
3     0.9474V
4     0.9773V
5     1.0089V
6     1.0388V
7     1.0757V
8     1.1056V
9     1.1021V
10    1.1109V
```          

### 当前电压           
```shell        
$ vcgencmd measure_volts
volt=1.1063V
```        

### 当前频率，获取所有CPU的频率
```shell         
$ sudo cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_cur_freq
2000000
$ sudo cat /sys/devices/system/cpu/cpu1/cpufreq/cpuinfo_cur_freq
2000000
$ sudo cat /sys/devices/system/cpu/cpu2/cpufreq/cpuinfo_cur_freq
2000000
$ sudo cat /sys/devices/system/cpu/cpu3/cpufreq/cpuinfo_cur_freq
2000000
```           
默认单位为 Hz 。         

### 当前温度          
```shell     
$ vcgencmd measure_temp      
temp=70.0'C           
```          

### 压力测试      
```shell       
$ sudo apt install stress
$ stress -c 4 -t 10m -v
```        

### 性能测试           
表示创建4个线程来寻找 √￣20000 以内的质数：         
```shell
$ sudo apt-get install sysbench
$ sysbench --num-threads=4 --test=cpu --cpu-max-prime=20000 run
```      
运行中树莓派的温度已经超过80度：
```shell         
$ vcgencmd measure_temp      
temp=86.0'C               
$ vcgencmd measure_temp
temp=85.0'C      
$ vcgencmd measure_temp     
temp=84.0'C 
$ vcgencmd measure_temp      
temp=85.0'C
```        