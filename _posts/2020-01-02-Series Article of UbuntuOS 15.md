---
layout:     post
title:      Series Article of UbuntuOS -- 15       
subtitle:   由于没有公钥，无法验证下列签名***及其他常见 apt 问题的解决方案                
date:       2021-09-11
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
---

## 无法验证签名       
使用 apt-get 安装软件或更新系统的时候，可能会爆出类似如下形式的错误：      
```bash       
W: GPG 错误：http://ppa.launchpad.net precise Release: 由于没有公钥，无法验证下列签名： NO_PUBKEY 3EE66BD3F599ACE3        
W: GPG 错误：http://ppa.launchpad.net precise Release: 由于没有公钥，无法验证下列签名： NO_PUBKEY 6AF0E1940624A220       
W: 无法下载 bzip2:/var/lib/apt/lists/partial/mirrors.163.com_ubuntu_dists_precise_main_binary-i386_Packages  Hash 校验和不符       
W: 无法下载 bzip2:/var/lib/apt/lists/partial/mirrors.163.com_ubuntu_dists_precise-security_main_binary-i386_Packages  Hash 校验和不符       
W: 无法下载 bzip2:/var/lib/apt/lists/partial/extras.ubuntu.com_ubuntu_dists_precise_main_binary-amd64_Packages  Hash 校验和不符      
W: 无法下载 bzip2:/var/lib/apt/lists/partial/extras.ubuntu.com_ubuntu_dists_precise_main_binary-i386_Packages  Hash 校验和不符      
E: Some index files failed to download. They have been ignored, or old ones used instead.      
```       

解决方法很简单，下载导入公钥就行，下载导入key的命令如下：        
```bash     
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys xxxxx
```     
此处的 xxxxx 是错误信息中提示缺少的公钥，如 3EE66BD3F599ACE3 。      

## 其他 apt 相关问题      
### 缺少公钥       
现象：     
```bash     
W: GPG error: http://apt.tt-solutions.com dapper Release: 由于没有公钥，下列签名无法进行验证： NO_PUBKEY 06EA41DE4F6C1E86
```     

解决方案：     
```bash      
gpg --keyserver subkeys.pgp.net --recv 4F6C1E86
gpg --export --armor 4F6C1E86 | sudo apt-key add -
```     

说明：
若缺少其他公钥，则将命令中两处4F6C1E86改为NO_PUBKEY 06EA41DE4F6C1E86中最后8位即可！

如果是Ubuntu PPA的则按照如下方法处理：     
```    
sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com
```    

### 签名错误      
**现象1：**    
```
By simply waiting. This happens at times during archive updates.
```     
解决方案：    
```bash    
$ sudo apt-get update -o Acquire::http::No-Cache=True   
OR   
$ sudo apt-get update -o Acquire::BrokenProxy=true     
```      

**现象2：**     
```     
In a particular case this was caused by a broken file and could get fixed using rescue boot and "fsck -fy /" (http://forum.ubuntuusers.de/goto?post=89197 - german)
```     

解决方案：     
```bash    
$ fsck -fy /     
```     

### source.list 报错      
**现象：**         
```     
The fix is just to back up sources.list, delete everything in it and run "apt-get update". After the update replace sources.list with the backup and run "apt-get update" again. You should not get the error then.
```      
解决方案：      
备份sources.list，然后把sources.list中的东西删空，运行"apt-get update"，然后再用刚刚的备份将"apt-get update"复原，再运行"apt-get update"。      

解决方案2：      
```bash    
$ sudo bash    
$ apt-get clean
$ cd /var/lib/apt
$ mv lists lists.old
$ mkdir -p lists/partial
$ apt-get clean
$ apt-get update
```      

### 缓存代理服务器导致的问题     
解决方案：     
如下内容添加到/etc/apt/apt.conf.d/10broken_proxy文件里：     
```    
Acquire::http::No-Cache "true";
Acquire::http::Max-Age "0";
```