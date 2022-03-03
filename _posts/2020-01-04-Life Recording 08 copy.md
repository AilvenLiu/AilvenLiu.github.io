---
layout:     post
title:      Life Recording -- 09 
subtitle:   Linux 配置 V2ray 客户端     
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space     
    - proxy 
---

1. 准备好 Q-v2ray 客户端（2.7之后停止更新）和 linux-64bits 版本的 v2ray-core（4.28.2之后转为 Go，无法使用）。       
   ```bash
   wget https://github.com/v2ray/v2ray-core/releases/download/v4.28.2/v2ray-linux-64.zip          
   wget https://github.com/Qv2ray/Qv2ray/releases/download/v2.7.0/Qv2ray.v2.7.0.linux-x64.AppImage          
   ``` 

2. 解压内核。         
   ```bash      
   unzip v2ray-linux-64.zip
   ```       

3. 赋予Qv2ray 可执行权限，最好转移到桌面，方便双击运行。      
   ```bash     
   chmod +x Qv2ray.v2.7.0.linux-x64.AppImage        
   ```       

4. Qv2ray主界面 --> 首选项，前四个界面设置如图：       
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/v2ray01.png"></div>      
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/v2ray02.png"></div>      
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/v2ray03.png"></div>      
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/v2ray04.png"></div>      

   可执行文件和资源路径，分别是解压内核路径下的 v2ray 文件 和解压的内核路径。     

5. 系统代理。      
   一般情况不用，如果有需要，配置如图：        
   <div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/life/proxy02.png"></div>      
   也即，只配置 HTTP 和 SOCKET 项。端口设置和 4 中的入站设置保持一致即可。    