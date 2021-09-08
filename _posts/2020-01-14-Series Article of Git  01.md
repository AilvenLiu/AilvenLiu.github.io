---
layout:     post
title:      Series Article of Git -- 01
subtitle:   Support for password authentication was removed on ...  以及免密配置        
date:       2021-09-08
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Git     
    - Github   
---     

一个多月没更新博客，今天（8thSep2021）再次更新的时候，使用 `git push` 提交报错：     
```    
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.       
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
```      
这是因为自2021年8月13号开始，github的接入规则发生了变化。解决方案转载自 [stackoverflow](https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to)：      

From August 13, 2021, GitHub is no longer accepting account passwords when authenticating Git operations. You need to add a PAT (Personal Access Token) instead, and you can follow the below method to add a PAT on your system.      

## Create Personal Access Token on GitHub     
From your GitHub account, go to      
**Settings => Developer Settings => Personal Access Token => Generate New Token (Give your password) => Fillup the form => click Generate token => Copy the generated Token**,      

it will be something like ghp_sFhFsSHhTzMDreGRLjmks4Tzuzgthdvfsrta     
Mine is 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      



## Add PAT    

### For WinOS     
Go to Credential Manager from Control Panel => Windows Credentials => find git:https://github.com => Edit => On Password replace with with your GitHub Personal Access Token => You are Done.     

If you don’t find git:https://github.com => Click on Add a generic credential => Internet address will be git:https://github.com and you need to type in your username and password will be your GitHub Personal Access Token => Click Ok and you are done.      

### For Linux-based OS     

For Linux, you need to configure the local GIT client with a username and email address.      
```bash     
$ git config --global user.name "your_github_username"      
$ git config --global user.email "your_github_email"     
$ git config -l     
```     
Once GIT is configured, we can begin using it to access GitHub. Example:      

```bash     
$ git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY       
> Cloning into `Spoon-Knife`...      
$ Username for 'https://github.com' : username     
$ Password for 'https://github.com' : give your personal access token here     
```    


实际上就是在验证过程中用 PAT 代替了密码