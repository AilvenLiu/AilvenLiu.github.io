---
layout:     post
title:      Series Article of Deep Learning -- 05
subtitle:   Ubuntu1804安装NVIDIA显卡驱动     
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space        
    - Ubuntu OS
---

### 查看显卡型号并下载相应驱动             
```bash       
lspci | grep -i nvidia
lspci | grep -i vge
```       
以上两条指令都可以，将打印出如下内容：      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia01.png"></div>      

每行最后，device 后面的编号即代表显卡型号。在 [PCI device](http://pci-ids.ucw.cz/mods/PC/10de?action=help?help=pci) 网站查询该编号，即可以得到对应的显卡型号。      
获取到显卡型号后，即可以在 [NVIDIA 官网](https://www.nvidia.com/Download/index.aspx) 下载对应的驱动。linux 不要选择 aarch64，要选择 64-bits 版本。如没有特殊需求，最新版本即可。         

### 禁用 nouveau 驱动           
1. 使用下述命令可以查看 nouveau 驱动是否运行：        
   ```bash     
   lsmod | grep nouveau
   ```        
   
   若出现下述结果：          
   ```
   nouveau              1863680  9       
   video                  49152  1 nouveau          
   ttm                   102400  1 nouveau          
   mxm_wmi                16384  1 nouveau         
   drm_kms_helper        180224  1 nouveau         
   drm                   479232  12 drm_kms_helper,ttm,nouveau         
   i2c_algo_bit           16384  2 igb,nouveau          
   wmi                    28672  4 intel_wmi_thunderbolt,wmi_bmof,mxm_wmi,nouveau         
   ```        
   说明 nouveau 驱动正在运行。            

2. 运行下述命令禁用该驱动：      
   ```bash
   sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"         
   sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"        
   ```        
   检查命令是否正确：          
   ```bash         
   cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf        
   ```         
   若出现下述结果说明命令正确：
   ```bash
   blacklist nouveau        
   options nouveau modeset=0         
   ```         
3. 更新设置并重启：          
   ```bash          
   sudo update-initramfs -u           
   sudo reboot          
   ```        
4. 重启后重新输入下述命令：       
   ```bash     
   lsmod | grep nouveau           
   ```          
   若没有任何输出说明禁用 nouveau 驱动成功。          

### 安装 NVIDIA 显卡驱动          
安装依赖：      
```bash
sudo apt install gcc g++ make
```        

安装驱动：      
```bash        
sudo bash ./NVIDIA-Linux-x86_64-xxx.xx.run       
```      
按照以下项选择：          

<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia02.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia03.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia04.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia05.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia06.png"></div>      
<div align=center><img src="https://raw.githubusercontent.com/OUCliuxiang/OUCliuxiang.github.io/master/img/deepL/nvidia07.png"></div>      

可能需要重启完成安装，但大部分情况不需要。完成安装后输入 nvidia-smi，若有如下输出，证明驱动安装成功：        
```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.56       Driver Version: 418.56       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 208...  Off  | 00000000:19:00.0 Off |                  N/A |
| 52%   57C    P0    59W / 250W |      0MiB / 10989MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  GeForce RTX 208...  Off  | 00000000:1A:00.0 Off |                  N/A |
| 73%   70C    P0    73W / 250W |      0MiB / 10989MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   2  GeForce RTX 208...  Off  | 00000000:67:00.0 Off |                  N/A |
| 79%   71C    P0    86W / 250W |      0MiB / 10989MiB |      1%      Default |
+-------------------------------+----------------------+----------------------+
|   3  GeForce RTX 208...  Off  | 00000000:68:00.0 Off |                  N/A |
| 44%   71C    P0     1W / 250W |      0MiB / 10986MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```