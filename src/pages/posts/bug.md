---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'WEB-bug'
pubDate: 2022-05-23
description: '文件上传'  ## 文章摘要
author: 'Snark'
cover:
    url: 'https://cdn.jsdelivr.net/gh/emyia2001/image@main/test/202303211456525.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["WEB", "文件上传", "CTF"]
theme: 'dark'  ## 主题背景色，light或dark
featured: false  ## 是否特色文章
---

# WP

### 解题过程

打开题目，提示让登录，看了下有登录/注册/找回密码功能点

注册一个账号登陆上看看，有管理/个人资料/修改密码/退出登录功能点

#### cookie

有user字段，使用的UUID（32位随机码）

#### 管理

提示不是admin，不能使用
根据uuid判断身份

#### 个人资料

显示了UID，Username，Birthday，Address
尝试修改UID进行越权，被提示没有权限，（后端有检测
尝试了sql注入，都不会执行

#### 修改密码

修改密码的身份和cookie绑定，不可越权

找不到什么漏洞，回去看看登录前的功能点

#### 登录点

测试了一下，存在账号admin
不存在sql注入
注册点

不能覆盖注册，会检测用户名是否存在
也不能用admin 注册
找回密码功能点

尝试修改前面注册的账号的密码，验证生日和地址后会跳转，猜测可能存在任意密码重置

修改密码，抓包，发现用username表示身份

![](https://i.328888.xyz/2023/03/17/LGF4t.png)

修改为username=admin，成功修改密码

登录之后点击manage功能点，提示IP Not allowed，

使用XFF伪造ip地址后成功进入manage功能点，但是没有flag = =
给了个注释

``` index.php?module=filemanage&do=??? ```
index.php?module=filemanage&do=???

估计wp知道是upload，打开是文件上传模块

![](https://i.328888.xyz/2023/03/17/LGzvX.png)

上传图片，提示

![](https://i.328888.xyz/2023/03/17/LGGIJ.png)

上传一句话shell，提示

![](https://i.328888.xyz/2023/03/17/LGddc.png)

有以下限制条件

文件后缀不能是php
文件内容不能含有php代码标签<?php xxx ?>

最后用这个shell上传

```
<script language="php">system($_GET['xxx']);</script>
```

再更改php后缀

![](https://i.328888.xyz/2023/03/17/LGhuA.png)