---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'MISC-helloshark'
pubDate: 2023-03-13
description: '平平无奇的一道杂项'
author: 'Snark'
cover:
    url: '../../../public/images/2.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["CTF", "MISC", "刷题"]
theme: 'dark'
featured: true
---

# WP

## 解题思路

首先下载附件

解压后发现是个.bmp的图片，直接拖进kali用foremost分离，但是我这里是用的7z直接就点开了

![](https://i.328888.xyz/2023/03/16/K53DP.png)

打开后发现需要解压密码，根据他的提示可知解压密码就在我们之前那个.bmp图片里

这里用zsteg这个工具分析这个图片

![|inline](https://i.328888.xyz/2023/03/16/K5cSt.png)

发现有个password，应该就是解压密码

![|inline](https://i.328888.xyz/2023/03/16/K5R3X.png)

解压出来后直接看流量包，跟踪TCP流，最终找到一条比较可疑的数据流

![|big](https://i.328888.xyz/2023/03/16/K5piJ.png)

多观察发现这一列有一串flag的字样,最终拼接出来就是flag的基本格式的样子，提交，完成

![|inline](https://i.328888.xyz/2023/03/16/K5Bbc.png)