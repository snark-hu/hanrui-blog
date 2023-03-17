---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'MISC-GoodNight'
pubDate: 2023-03-13
description: '疯狂刷题ing'
author: 'Snark'
cover:
    url: '../../../public/images/1.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["CTF", "MISC", "刷题"]
theme: 'dark'
featured: true
---

# WP

Hint1:题目的附件名字很重要  
Hint2:flag的内容要转为md5 flag 格式flag{XXXXX}

## 解题思路

下载附件后发现是一个压缩包

![](https://i.328888.xyz/2023/03/17/LO0ep.png)

解开后发现是一个图片

![](https://i.328888.xyz/2023/03/17/LOCaU.png)

各种尝试后还是无果，看了一眼wp后才知道要用**oursecret**这个工具

---
**Our Secret文件加密软件**是一款操作便捷的文件加密工具，它能够帮助用户轻松把秘密的文件等藏进让你看得到的图片、视频、文件等文档里面，需要密码才能解开的私密文档。

使用方法：<https://jingyan.baidu.com/article/c275f6ba1fe871e33d75671a.html>

---

根据提示得到密码，使用后提出了了一个附件

![](https://i.328888.xyz/2023/03/17/LOwkv.png)

用010打开，看到文件头是BP，网上搜索资料得bp开头的文件才知道这是一种新的图片格式bpg
头是425047FB， B190是文件尾

![](https://i.328888.xyz/2023/03/17/LO2B3.png)

但在文件尾并没有发现B190的十六进制，不过在文件尾发现了一段熟悉的十六进制，猜测可能数据被倒序，但是文件开头并没有被倒序，尝试搜索bpg文件尾B190，编写脚本进行倒序

![](https://i.328888.xyz/2023/03/17/LOHJy.png)

![](https://i.328888.xyz/2023/03/17/LObA5.png)

![](https://i.328888.xyz/2023/03/17/LOjr8.png)

需要密码，找了一圈没发现密码提示，直接爆破

![](https://i.328888.xyz/2023/03/17/LO85Z.png)

发现不能识别这个压缩包，而我本机上用7z能直接打开，最后用010打开才发现是缺少了文件头。(某些解压软件会忽略这些错误一样可以打开，比如WinRAR可以直接打开压缩包伪加密)

![](https://i.328888.xyz/2023/03/17/LONZF.png)

![](https://i.328888.xyz/2023/03/17/LO3eQ.png)

爆破得到压缩包密码qwerty

![](https://i.328888.xyz/2023/03/17/LOm0C.png)

![](https://i.328888.xyz/2023/03/17/LOpBP.png)

解压出来之后得到写着16进制的文本

![](https://i.328888.xyz/2023/03/17/LOBJX.png)

新建文本文件，将其复制进去新的文本再导入进去010，得到pcap流量包；也可以根据文件头分析得出是流量包；甚至在010另存为一个某后缀名的文件，直接放进Kali，也会自动识别出是一个流量包

![](https://i.328888.xyz/2023/03/17/LOgDt.png)

![|inline](https://i.328888.xyz/2023/03/17/LOxrJ.png)

![](https://i.328888.xyz/2023/03/17/LO75c.png)

解到这里也没有思路了，平常流量包做的少，还是太菜了，继续看wp，发现关键点在identification上

![](https://i.328888.xyz/2023/03/17/LOeiA.png)

将所有的字符给提取出来，注意把前面00给去掉，然后进行ascii转字符串，发现
```
@iH<,{*;oUp/im"QPl`yR
```
和前面重复了，所以要删除。

这里我使用我自己写的工具直接转的base91

![](https://i.328888.xyz/2023/03/17/LOzOo.png)

```
@iH<,{*;oUp/im"QPl`yR*ie}NK;.D!Xu)b:J[Rj+6KKM7P

flag{wMt84iS06mCbbfuOfuVXCZ8MSsAFN1GA}
```

因为题目要求提交flag的md5值，所以转一下md5得到flag

```
flag{4e5478269e04a597fc2381ecafe16080}
```