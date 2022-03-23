---
layout:     post
title:      Series Article of Deep Learning -- 07
subtitle:   conda 和 pip 换清华源    
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space        
    - Ubuntu OS
---
### conda 换清华源         
编辑 `~/.condarc` 为以下内容并保存：           
```
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```       

即可添加 `Anaconda Python` 免费仓库。          
运行 `conda clean -i` 清除索引缓存，保证用的是镜像站提供的索引。           

### pip 换清华源        
升级 pip 到最新的版本 (>=10.0.0) 后进行配置：       
```bash
pip install pip -U          
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple        
```       
如果您到 pip 默认源的网络连接较差，临时使用本镜像站来升级 pip：          
```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U         
```