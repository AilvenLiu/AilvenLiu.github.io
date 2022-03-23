---
layout:     post
title:      Series Article of Git -- 02
subtitle:   github hosts 加速        
date:       2022-03-03
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Ubuntu OS
    - Github   
    - Work Space
---     

通过修改 hosts 文件方式加速 github 的访问。[github520](https://github.com/521xueweihan/GitHub520) 项目提供了定时更新的 hosts 数据。截至，2022年3月3日，最新的 hosts 内容如下：           
```
# GitHub520 Host Start
140.82.113.26                 alive.github.com
140.82.114.26                 live.github.com
185.199.108.154               github.githubassets.com
140.82.112.21                 central.github.com
185.199.108.133               desktop.githubusercontent.com
185.199.108.153               assets-cdn.github.com
185.199.108.133               camo.githubusercontent.com
185.199.108.133               github.map.fastly.net
199.232.69.194                github.global.ssl.fastly.net
140.82.112.4                  gist.github.com
185.199.108.153               github.io
140.82.112.3                  github.com
192.0.66.2                    github.blog
140.82.114.6                  api.github.com
185.199.108.133               raw.githubusercontent.com
185.199.108.133               user-images.githubusercontent.com
185.199.108.133               favicons.githubusercontent.com
185.199.108.133               avatars5.githubusercontent.com
185.199.108.133               avatars4.githubusercontent.com
185.199.108.133               avatars3.githubusercontent.com
185.199.108.133               avatars2.githubusercontent.com
185.199.108.133               avatars1.githubusercontent.com
185.199.108.133               avatars0.githubusercontent.com
185.199.108.133               avatars.githubusercontent.com
140.82.114.10                 codeload.github.com
54.231.134.49                 github-cloud.s3.amazonaws.com
52.217.164.49                 github-com.s3.amazonaws.com
52.217.164.49                 github-production-release-asset-2e65be.s3.amazonaws.com
52.216.134.83                 github-production-user-asset-6210df.s3.amazonaws.com
52.217.105.188                github-production-repository-file-5c1aeb.s3.amazonaws.com
185.199.108.153               githubstatus.com
64.71.144.202                 github.community
23.100.27.125                 github.dev
140.82.113.22                 collector.github.com
199.232.68.133                pipelines.actions.githubusercontent.com
185.199.108.133               media.githubusercontent.com
185.199.108.133               cloud.githubusercontent.com
185.199.108.133               objects.githubusercontent.com


# Update time: 2022-03-03T16:05:54+08:00
# Update url: https://raw.hellogithub.com/hosts
# Star me: https://github.com/521xueweihan/GitHub520
# GitHub520 Host End
```         

将以上内容或从 [github520](https://github.com/521xueweihan/GitHub520) 获取到的最新 hosts 复制到 `/etc/hosts` 并保存（需 sudo），一般情况下会立即生效，若没有立即生效，使用以下命令：          
```bash      
sudo nscd restart
```     
手动更新。若提示 nscd 不存在，apt 安装即可；若报错 nscd未启动或不合规等错误，使用该命令：          
```bash   
sudo /etc/init.d/nscd restart
```          
重启即可。     