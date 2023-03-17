---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'MISC-喵喵喵'
pubDate: 2023-03-13
description: '平平无奇的一道杂项'
author: 'Snark'
cover:
    url: 'https://w.wallhaven.cc/full/jx/wallhaven-jxep8y.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["CTF", "MISC", "刷题"]
theme: 'dark'
featured: true
---

# WP

## 解题思路

打开附件是一个png图片，常规的文件分离没有任何文件，只有单张图片，打开010分析也没有任何报错，最后发现是LSB隐写

![|inline](https://s2.xptou.com/2023/03/17/641423fb48478.png)

![|inline](https://s2.xptou.com/2023/03/17/641423fba99b1.png)

![|inline](https://s2.xptou.com/2023/03/17/641423fbef44b.png)

发现**RGB**三个色道里面**0**通道里面是藏了东西，直接提取出来

![](https://s2.xptou.com/2023/03/17/641423fcb50df.png)

用010打开后发现文件头多了几个字符FFFE，删除得到半截二维码图片

![](https://s2.xptou.com/2023/03/17/641423fdba3d4.png)

打开010后有报错

![](https://s2.xptou.com/2023/03/17/641424004fcae.png)

直接修改高度（这里010报错是因为我们没有修改到正确的高度，但是并不影响图片显示）

![](https://s2.xptou.com/2023/03/17/641424dcc194c.png)

解出了一个百度网盘的地址：https://pan.baidu.com/s/1pLT2J4f

下载后得到flag.rar文件，这里有一个巨坑，某些解压软件会**忽略NTFS流**，比如当时我用的7z。所以当时我怎么都没有做出来，而后来用了WinRAR就可以了

![](https://s2.xptou.com/2023/03/17/641424e97f9dc.png)

这里我们看到使用WinRAR的时候会报错

![](https://s2.xptou.com/2023/03/17/641423ffad193.png)

最后使用这个工具NTFSstreamseditor，提取出NTFS流，隐藏的是一个pyc文件

![|inline](https://s2.xptou.com/2023/03/17/6414243413ad6.png)

   pyc文件是**一种二进制文件，是由py文件经过编译后，生成的文件**，是一种byte code字节码，py文件变成pyc文件后，加载的速度有所提高，而且pyc是一种跨平台的字节码，是由Python的虚拟机来执行的，这个是类似于Java或者. NET的虚拟机的概念，从而实现跨平台

使用在线的工具对pyc文件进行反编译

> 在线网站：https://tool.lu/pyc/

![](https://s2.xptou.com/2023/03/17/64142434e2258.png)

```python
#!/usr/bin/env python
# visit https://tool.lu/pyc/ for more information
# Version: Python 2.7

import base64

def encode():
    flag = '*************'
    ciphertext = []
    for i in range(len(flag)):
        s = chr(i ^ ord(flag[i]))
        if i % 2 == 0:
            s = ord(s) + 10
        else:
            s = ord(s) - 10
        ciphertext.append(str(s))
    
    return ciphertext[::-1]

ciphertext = [
    '96',
    '65',
    '93',
    '123',
    '91',
    '97',
    '22',
    '93',
    '70',
    '102',
    '94',
    '132',
    '46',
    '112',
    '64',
    '97',
    '88',
    '80',
    '82',
    '137',
    '90',
    '109',
    '99',
    '112']
```

写个脚本解密

```python
def decode(arg1):
	ciphertext = arg1[::-1]
	flag = ''
	for i in range(len(ciphertext)):
		if i % 2 == 0:
			s = int(ciphertext[i]) - 10
		else:
			s = int(ciphertext[i]) + 10 
		s = s ^ i
		flag += chr(s)
	print(flag)

if __name__ == '__main__':
	ciphertext = [
    '96',
    '65',
    '93',
    '123',
    '91',
    '97',
    '22',
    '93',
    '70',
    '102',
    '94',
    '132',
    '46',
    '112',
    '64',
    '97',
    '88',
    '80',
    '82',
    '137',
    '90',
    '109',
    '99',
    '112']
	decode(ciphertext)

```

    flag{Y@e_Cl3veR_C1Ever!} 