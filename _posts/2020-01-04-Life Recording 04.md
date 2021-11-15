---
layout:     post
title:      Life Recording -- 04 
subtitle:   使用 uBlacklist 屏蔽谷歌搜索（简体中文）垃圾结果     
date:       2021-11-15
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Life       
---
中文网络世界越来越恶心了，随便搜一点程序设计相关的东西，就是成百上千的“小 X 百科网”、“小 X 知识网”，恶心。       
浏览器下载添加 uBlacklisk 插件，[Chrome Web Store](https://chrome.google.com/webstore/detail/ublacklist/pncfbmialoiaghdehhbnbhkkgmjanfhe)，[Firefox Add-ons](https://addons.mozilla.org/en-US/firefox/addon/ublacklist/)，[Mac App Store](https://apps.apple.com/app/ublacklist-for-safari/id1547912640)。将以下 2 个链接，通过点击 Add a subscription 添加到 Subscription 分类下。      
1. [精确匹配](https://raw.githubusercontent.com/cobaltdisco/Google-Chinese-Results-Blocklist/master/uBlacklist_subscription.txt)：该匹配方式主要是通过 `*://*.xxxx.com/*` 的方式来匹配搜索结果，进行过滤。基本不会有误杀。       
2. [模糊匹配](https://raw.githubusercontent.com/cobaltdisco/Google-Chinese-Results-Blocklist/master/uBlacklist_match_patterns.txt)：该匹配方式主要是通过如 `//*/list.php?s=*`、`title/小.(百科|知识)网/` 的方式来匹配搜索结果，进行过滤。存在小范围的误杀。

请仔细查看当前模糊匹配列表如下。若会击中自己经常使用的网站，或屏蔽不完整，可自行修改规则配置到插件中，防止误杀或漏检。        
```
*://*/so.php
*://*/so.php?s=*
*://*/cha.php?s=*
*://*/list.php?s=*
*://*/?s=*
*://*/so/*
title/^小.(百科|知识)网$/
title/^.*[ ]-[ ]小.(百科|知识)网$/
title/.*点击一次就可以出国/
title/.*一键访问国外网站/
```     