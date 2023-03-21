---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'proxychains命令行代理'
pubDate: 2022-07-19
description: '如何配置命令行代理'
author: 'Snark'
cover:
    url: 'https://cdn.jsdelivr.net/gh/emyia2001/image@main/test/202303211456531.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["Tools", "工具", "代理"]
theme: 'dark'
featured: true
---


# 设置ProxyChains

ProxyChains是Linux和其他Unices下的代理工具。它可以使任何程序通过代理上网，允许TCP和DNS通过代理隧道，支持HTTP、SOCKS4和SOCKS5类型的代理服务器，并且可配置多个代理。ProxyChains通过一个用户定义的代理列表强制连接指定的应用程序，直接断开接收方和发送方的连接。本节将介绍设置ProxyChains的方法。

设置ProxyChains的具体操作步骤如下所示。

（1）打开ProxyChains配置文件。执行命令如下所示：

```
vim /etc/proxychains.conf
```

执行以上命令后，打开文件的内容如下所示：

```
# proxychains.conf VER 3.1
#
# HTTP, SOCKS4, SOCKS5 tunneling proxifier with DNS.
#
# The option below identifies how the ProxyList is treated.
# only one option should be uncommented at time,
# otherwise the last appearing option will be accepted
#
#dynamic_chain
#
# Dynamic - Each connection will be done via chained proxies
# all proxies chained in the order as they appear in the list
# at least one proxy must be online to play in chain
# (dead proxies are skipped)
# otherwise EINTR is returned to the app
#
strict_chain
#
# Strict - Each connection will be done via chained proxies
# all proxies chained in the order as they appear in the list
# all proxies must be online to play in chain
# otherwise EINTR is returned to the app
#
#random_chain
#
# Random - Each connection will be done via random proxy
# (or proxy chain, see chain_len) from the list.
# this option is good to test your IDS :)
```

输出的信息就是proxychains.conf文件的内容。由于篇幅的原因，这里只列出了部分内容。

（2）将proxychains.conf文件中的dynamic_chain前面的注释符取消。要修改的配置项，是上面加粗的部分，如下所示：

```
dynamic_chain
```

（3）添加一些代理服务器到列表（proxychains.conf文件末尾），如下所示：

![](https://i.328888.xyz/2023/03/17/LIG8w.png)

红框内填写代理服务器IP及端口，保存退出。

（4）查看是否代理成功，执行命令如下所示：

```
proxychains curl ipinfo.io
```

![](https://i.328888.xyz/2023/03/17/LIdFa.png)

发现地址已经变成了台湾，说明代理成功了