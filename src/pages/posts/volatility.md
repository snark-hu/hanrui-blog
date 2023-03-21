---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'Volatility'
pubDate: 2022-03-22
description: '常用的内存取证工具'  ## 文章摘要
author: 'Snark'
cover:
    url: 'https://cdn.jsdelivr.net/gh/emyia2001/image@main/test/202303211456533.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["Tools", "内存取证", "Volatility", "工具"]
theme: 'dark'  ## 主题背景色，light或dark
featured: true  ## 是否特色文章
---

# volatility

# 一.   工具的基本使用

### 1.1  下载安装

官网下载：<https://www.volatilityfoundation.org/releases>

github下载：

python2版本  https://github.com/volatilityfoundation/volatility

python3版本 https://github.com/volatilityfoundation/volatility3

这里就使用python2版本的volatility演示，使用如下命令安装。

```
git clone https://github.com/volatilityfoundation/volatility.git

cd volatility

pip install pycryptodome

pip install distorm3

python2 setup.py install

vol.py -h
```

### 1.2  命令格式

命令格式

```
vol.py -f [image] --profile=[profile] [plugin]
```

在分析之前，首先需要知道当前的镜像信息，通过imageinfo命令分析是哪个系统。

```
vol.py -f xx.vmem imageinfo
```

### 1.3  常用插件

查找⽤户名密码信息。当前操作系统中的 password hash，例如 Windows 的 SAM 文件内容

```
vol.py -f Target.mem --profile=Win7SP0x64 hashdump
```

从注册表中提取LSA密钥信息(已解密)

```
vol.py -f Target.mem --profile=Win7SP0x64 lsadump
```

列举出系统进程，但它不能检测到隐藏或者解链的进程

```
vol.py -f Target.mem --profile=Win7SP0x64 pslist
```

可以找到先前已终止(不活动)的进程以及被rootkit隐藏或解链的进程

```
vol.py -f Target.mem --profile=Win7SP0x64 psscan
```

以树的形式查看进程列表，和pslist一样，也无法检测隐藏或解链的进程

```
vol.py -f Target.mem --profile=Win7SP0x64 pstree
```

提取进程, -p是进程号 -D 存储的文件夹 提取出指定进程

```
vol.py -f Target.mem --profile=Win7SP0x64 memdump -p xxx --dump-dir=./
```

查看服务

```
vol.py -f Target.mem --profile=Win7SP0x64 svcscan
```

查看ie浏览器浏览历史

```
vol.py -f Target.mem --profile=Win7SP0x64 iehistory
```

查看⽹络连接

```
vol.py -f Target.mem --profile=Win7SP0x64 netscan
```

查看命令⾏操作

```
vol.py -f Target.mem --profile=Win7SP0x64 cmdscan
```

查看⽂件

```
vol.py -f Target.mem --profile=Win7SP0x64 filescan
```

提取文件

```
vol.py -f Target.mem --profile=Win7SP0x64 dumpfiles -Q 0xxxxxxxx -D ./
```

查看当前展示的notepad内容

```
vol.py -f Target.mem --profile=Win7SP0x64 notepad
```

查看屏幕截图

```
vol.py -f Target.mem --profile=Win7SP0x64 screenshot --dump-dir=./
```

查看注册表单元

```
vol.py -f Target.mem --profile=Win7SP0x64 hivelist
```

导出注册表, -o 注册表的virtual地址

```
vol.py -f Target.mem --profile=Win7SP0x64 hivedump -o 0xfffff8a001032410
```

获取注册表信息，-K “键值”

```
vol.py -f Target.mem --profile=Win7SP0x64 printkey -K "xxxxxxx"
```

查看运⾏程序相关的记录

```
vol.py -f Target.mem --profile=Win7SP0x64 userassist
```

在环境变量中查找KEY值

```
vol.py -f Target.mem --profile=Win7SP0x64 envars | grep "KEY"
```

抓取控制台下执行的命令以及回显数据

```
vol.py -f Target.mem --profile=Win7SP0x64 consoles
```

