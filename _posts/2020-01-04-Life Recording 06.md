---
layout:     post
title:      Life Recording -- 06 
subtitle:   ubuntu 下 vscode 禁用 gpu 加速 / 节约显存空间     
date:       2021-11-24
author:     OUC_LiuX
header-img: img/wallpic02.jpg
catalog: true
tags:
    - Work Space       
---

ubuntu 系统中 vscode 默认 gpu enable，如果打开窗口过多，编辑文件过长，很容易出现卡顿的情况。可以通过添加 `--disable-gpu` 使禁用 gpu 加速。                         

### 从命令行启动vscode              
对于习惯从命令行启动 vscode 的用户，在 .bashrc 或者 .zshrc 添加：         
```bash 
alias code="code --disable-gpu"
```        

### 使用系统图标，点击启动vscode            
需要修改 `/usr/share/applications/code.desktop`。          
使用deb文件安装的 vscode，一般都放在 `/usr/share/application` 目录下。
也可能在 `~/.local/share/applications` 下。             

下面是修改过后的文件， 就是在 `Exec=/usr/share/code/code` 后面添加 `--disable-gpu` 即可。             

```conf
[Desktop Entry]
Name=Visual Studio Code
Comment=Code Editing. Redefined.
GenericName=Text Editor
Exec=/usr/share/code/code --disable-gpu -no-sandbox --unity-launch %F
Icon=/usr/share/pixmaps/com.visualstudio.code.png
Type=Application
StartupNotify=false
StartupWMClass=Code
Categories=Utility;TextEditor;Development;IDE;
MimeType=text/plain;inode/directory;
Actions=new-empty-window;
Keywords=vscode;

X-Desktop-File-Install-Version=0.24

[Desktop Action new-empty-window]
Name=New Empty Window
Exec=/usr/share/code/code  --disable-gpu --no-sandbox --new-window %F
Icon=/usr/share/pixmaps/com.visualstudio.code.png
```