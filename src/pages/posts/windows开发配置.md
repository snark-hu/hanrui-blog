---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'windows开发配置'
pubDate: 2022-05-19
description: '我的Windows开发环境配置'
author: 'Snark'
cover:
    url: '../../../public/images/5.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["开发环境", "WSL", "scoop", "Windows开发环境搭建", "powershell美化"]
theme: 'dark'
featured: true
---


## powershell美化

#### fzf文件搜索

```
# 安装fzf
scoop install fzf
```
#### 添加用户配置文件
把user_profile.ps1、command_profile.ps1文件创建到 "用户目录\.config\powershell" 下
使用$profile查看PowerShell配置文件，打开文件添加以下命令
#### oh-my-posh
```
# 安装oh-my-posh
scoop install oh-my-posh

# 在用户配置文件中添加
oh-my-posh --init --shell pwsh --config C:\Users\31438\scoop\apps\oh-my-posh\current\themes\unicorn.omp.json | Invoke-Expression
```
#### Terminal-Icons
```
# 安装Terminal-Icons
scoop install terminal-icons

# 在用户配置文件下添加
Import-Module -Name Terminal-Icons
```
#### posh-git
```
# 安装posh-git
scoop install posh-git

# 在用户配置文件下添加
Import-Module posh-git
```
#### PSReadLine
查看PSReadLine的全部函数

```
Get-PSReadLineKeyHandler
```

安装

```
# 安装PSReadLine
Install-Module -Name PSReadLine -AllowPrerelease -Force
```
为PowerShell配置相关快捷键
:::info
配置文件$Profile
:::
```
# PSReadLine配置
Set-PSReadLineOption -PredictionSource History # 设置预测文本来源为历史记录
Set-PSReadLineOption -HistorySearchCursorMovesToEnd # 选择历史命令时，光标移动到末尾
Set-PSReadLineKeyHandler -Key Tab -Function MenuComplete # 设置 Tab 为菜单补全和 Intellisense
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo # 设置 Ctrl+z 为撤销
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward # 设置向上键为后向搜索历史记录
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward # 设置向下键为前向搜索历史纪录
Set-PsReadLineKeyHandler -key "Ctrl+Shift+a" -Function BeginningOfLine # 将光标移动到行首
Set-PsReadLineKeyHandler -key "Ctrl+a" -Function BackwardWord # 向前移动一个单词
# 将光标移动到行尾，并能补全历史命令
Set-PSReadLineKeyHandler -Chord "Ctrl+Shift+e" -ScriptBlock {
    [Microsoft.PowerShell.PSConsoleReadLine]::AcceptSuggestion()
    [Microsoft.PowerShell.PSConsoleReadLine]::EndOfLine()
}
Set-PsReadLineKeyHandler -key "Ctrl+e" -Function ForwardWord # 向后移动一个单词
# 定义颜色
Set-PSReadLineOption -Colors @{
  Command            = 'Magenta'
  Number             = 'DarkBlue'
  Member             = 'DarkBlue'
  Operator           = 'DarkBlue'
  Type               = 'DarkBlue'
  Variable           = 'DarkGreen'
  Parameter          = 'DarkGreen'
  ContinuationPrompt = 'DarkBlue'
  Default            = 'DarkBlue'
  InlinePrediction   = 'DarkGray'
}
```
#### which 和 touch
在command_profile.ps1中添加
```
# which 和 touch
function which ($command) {
  Get-Command -Name $command -ErrorAction SilentlyContinue |
    Select-Object -ExpandProperty Path -ErrorAction SilentlyContinue
}
function touch($name){
  if ($name) {
    $file_path = Split-Path -Path $name
    $file_name = Split-Path -Path $name -Leaf
    if ($file_path -eq "") {
      $file_path = "."
    }
    if (-Not (Test-Path($file_path))) {
      New-Item -ItemType "directory" -Path $file_path
    }
    New-Item -Path $file_path -Name $file_name -ItemType "file"
  }
  else {
     Write-Host "Command to create new file."
  }
}
```
## scoop
#### 安装
```
# 一键安装scoop
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')

# 如果执行出错就是用这条命令后，再执行上一条命令
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```
**帮助文档**
[https://scoop-docs.vercel.app/](https://scoop-docs.vercel.app/)
## 系统环境配置
#### python3、python2.7
```
# 添加versions软件源
scoop bucket add versions

# 安装python
scoop install python python27

# 多版本切换
scoop reset 想要使用的版本
```
## vim
```
# 安装neovim
scoop install neovim
```
neovim扩展
[https://neovimcraft.com/](https://neovimcraft.com/)
#### 安装neovide
```
# 必须要有extras软件库
scoop install neovide
```
#### LunarVim
##### 安装PowerShell 7.2
[https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-onwindows?view=powershell-7.2](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-onwindows?view=powershell-7.2)
##### 安装依赖
```
scoop install nodejs
scoop install python
scoop install gcc
```
更换Python 和 NPM 源
Python
豆瓣源
```
pip config set global.index-url https://pypi.douban.com/simple/
```
Python with HTTP
豆瓣源
```
pip config set global.index-url http://pypi.douban.com/simple/
pip config set install.trusted-host pypi.douban.com
```
NPM
淘宝源
```
npm config set registry https://registry.npm.taobao.org
```
##### 安装 LunarVim
```
Invoke-WebRequest\
https://raw.githubusercontent.com/LunarVim/LunarVim/master/utils/installer/inst\
all.ps1 -UseBasicParsing | Invoke-Expression
```
根据需求选择完毕后
输入 lvim 进入 LunarVim 使用 :PackerSync 安装插件
##### NeoVide 结合 LunarVim
创建可执行文件
添加 ~/.local/bin 到环境变量 $PATH 中
进入 ~/.local/bin
将 lvim.ps1 复制为 lvide.ps1
```
cp lvim.ps1 lvide.ps1
```
编辑 lvide.ps1
将最后的一行代码替换成为
```
neovide -- -u "$env:LUNARVIM_RUNTIME_DIR\lvim\init.lua" @args
```
创建快捷方式
 进入 C:\Users\$env:USERNAME\AppData\Roaming\Microsoft\Windows\Start Menu\Programs 中 右键添加快捷方式，为 C:\Program Files\PowerShell\7\pwsh.exe 添加快捷方式  
命名为 Lvide
编辑 Lvide 属性  
在目标路径更改为 "C:\Users\%UserName%\.local\bin\lvide.ps1"
设置起始位为 "C:\Users\%UserName%\OneDrive\Desktop"
设置快捷键为 "Shift + Alt + P"
更改图标为 NeoVIM 的图标 "%USERPROFILE%\scoop\apps\neovim\current\bin\nvim-qt.exe"
##### 设置LunarVim字体
编辑LunarVim 配置文件 "C:\Users\%UserName%\AppData\Local\lvim\config.lua"
添加 vim.o.guifont = "Hack NF" 配置字体
##### 为命令配置文件添加
```
Set-Alias lvim C:\Users\31438\.local\bin\lvim.ps1
Set-Alias lvide C:\Users\31438\.local\bin\lvide.ps1
```
## wsl2
#### 安装
win键搜索 Windows Features打开 适用于linux的windows子系统 和 Hyper-V 和 虚拟机平台 然后重启电脑
使用微软商店安装 ubuntu 22.04即可
#### 迁移
在 设置 -> 应用 -> 应用和功能 -> linux发行版 -> 移动
#### 环境配置
##### 换源
```
# 打开源文件
sudo vim /etc/apt/sources.list

# 替换成阿里源
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse
```
##### apache2
```
# 安装apache2
sudo apt install apache2

# 开启服务
sudo service apache2 start

# 测试
curl 127.0.0.1
```
##### php
```
# 安装php
sudo apt install php libapache2-mod-php
```
##### mysql
```
# 安装Mysql
sudo apt install mysql-server
```
##### 配置mysql
```
# 运行保护mysql的脚本
sudo mysql_secure_installation
# 登录
sudo mysql -u root

# 创建用户
mysql> CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

# 赋予权限
mysql> GRANT ALL(select, insert, update, delete) ON databasename.tablename(* 为全部) TO 'username'@'localhost' WITH GRANT OPTION;
```


## java开发环境配置

### 下载JDK

下载 java 开发工具包 JDK，下载地址：[https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/)


## 生成SSH密钥登陆云服务器

### 生成SSH key

```
PowerShell 7.3.2
PS C:\Users\dh176> ssh-keygen -t rsa -b 4096 -C "192.168.1.1"
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\dh176/.ssh/id_rsa): key
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:Q2OhAt/RcZmaJ2pHX1WMDDsk40g7oLCJ+YkQNZaoYP4 192.168.1.1
The key's randomart image is:
+---[RSA 4096]----+
|.o*.  o.+.+ooo +.|
|o*.B o =.*o+ .+ .|
|O o + o Bo. o.   |
|o+ . . o=o. ..   |
|. +    oS+ .     |
|   E  o ...      |
|     . .         |
|                 |
|                 |
+----[SHA256]-----+
PS C:\Users\dh176>
```

**key是私钥，key.pub是公钥**

### 将公钥传输上云服务器

这里我使用的mobaxterm，用户root，密钥文件key.pub

![](https://i.328888.xyz/2023/03/17/LNAGJ.png)

## blog环境配置

### 安装git

直接在官网下载git：<https://git-scm.com/download/win>

打开Git Bash，设置user.name和user.email配置信息：

![|inline](https://i.328888.xyz/2023/03/17/LN4It.png)

```
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

生成ssh密钥文件：

```
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```

直接三个回车即可，默认不需要设置密码  
在.ssh的文件夹中的id_rsa.pub密钥，将内容全部复制传上自己的github

![](https://i.328888.xyz/2023/03/17/LNDqc.png)

![](https://i.328888.xyz/2023/03/17/LNn2A.png)

在Git Bash中检测GitHub公钥设置是否成功，输入 ssh git@github.com

![](https://i.328888.xyz/2023/03/17/LNt7o.png)

### 安装nodejs

```
scoop install nodejs
```

### 安装hexo

```
scoop install nodejs  #使用scoop安装nodejs
npm install -g hexo-cli  #安装hexo
```

#### hexo常用命令

```
npm install hexo -g #安装Hexo  
npm update hexo -g #升级  
hexo init #初始化博客  
  
命令简写  
hexo n "我的博客" == hexo new "我的博客" #新建文章  
hexo g == hexo generate #生成  
hexo s == hexo server #启动服务预览  
hexo d == hexo deploy #部署  
  
hexo server #Hexo会监视文件变动并自动更新，无须重启服务器  
hexo server -s #静态模式  
hexo server -p 5000 #更改端口  
hexo server -i 192.168.1.1 #自定义 IP  
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令
```