---
layout: '../../layouts/MarkdownPost.astro'  ## 布局文件
title: 'DNS服务搭建'
pubDate: 2022-05-23
description: '常见服务搭建'  ## 文章摘要
author: 'Snark'
cover:
    url: 'https://w.wallhaven.cc/full/yx/wallhaven-yx5vyk.jpg'  ## 宽屏图片
    square: ''  ## 方形图片
    alt: 'cover'
tags: ["服务搭建", "DNS", ]
theme: 'dark'  ## 主题背景色，light或dark
featured: false  ## 是否特色文章
---

# DNS服务配置

## 配置

1. 设置所有 Linux 服务器的时区设为“上海”，本地时间调整为实际时间。 

2. 启动所有 Linux 服务器的防火墙，并添加相应端口（不允许添加服务） 放行相关服务。 

3. 利用 chrony 配置 Linux-1 为其他 Linux 主机提供时间同步服务。 

4. 利用 bind9 软件，配置 Linux-1 为主 DNS 服务器，采用 rndc 技术提供 不间断的 DNS 服务；配置 Linux-2 为备用 DNS 服务器，为所有 Linux 主 机提供冗余 DNS 正反向解析服务。 

5. 所有 Linux 主机 root 用户使用完全合格域名免密码 ssh 登录到其他 Linux 主机。 

6. 配置 Linux-1 为 CA 服务器,为所有 Linux 主机颁发证书，不允许修改 /etc/pki/tls/openssl.conf。CA 证书有效期 20 年，CA 颁发证书有效 期均为 10 年，证书信息：国家=“CN”，省=“Beijing”，城市=“Beijing”， 组织=“skills”，组织单位=“system”。chrome 浏览器访问 https 网站 时，不出现证书警告提示信息。

## 初始配置

```plain
# 修改主机名
hostnamectl set-hostname 主机名
```

```
# 挂载镜像
mkdir /media/CentOS
mount -t iso9660 -o loop /dev/cdrom /media/CentOS
mount -a             #挂载
df -h              #查看挂载
```

![|inline](https://i.328888.xyz/2023/03/17/LEc9J.png)

```
# 配置IP
vim /etc/sysconfig/network-scripts/ifcfg-ens33  #ens33为网卡名
```

![|inline](https://i.328888.xyz/2023/03/17/LEpAc.png)

```
systemctl restart network      #重启network
systemctl status network       #查看状态
```
![|inline](https://i.328888.xyz/2023/03/17/LEBrA.png)

## 实验环境

Linux -1 172.16.100.254 服务端

Linux 172.16.100.201 客服端

### 设置所有linux服务器的时区设为“上海”，本地时间调整为实际时间。

```
timedatectl set-timezone Asia/Shanghai    #设置时区为上海
timedatectl 							  #验证
```

![|inline](https://i.328888.xyz/2023/03/17/LExZN.png)


![|inline](https://i.328888.xyz/2023/03/17/LEXho.png)

```
yum -y install ntpdate
service ntpdate restart
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtim
```

### 设置所有linux服务器的时区设为“上海”，本地时间调整为实际时间。

```
# 放行防火墙端口 Linux-1(服务端)
firewall-cmd --add-port==123/udp --permanent
firewall-cmd --reload
firewall-cmd --list-all|grep 123
```

### 利用chrony配置Linux-1为其他Linux主机提供时间同步服务。

centos默认安装了chrony（保证虚拟机之间能够通信）

服务端Linux -1 上配置：

```
vim /etc/chrony.conf          #修改以下内容 
```

![|inline](https://i.328888.xyz/2023/03/17/LE7OV.png)

```
systemctl restart chronyd.service     #重启服务
systemctl enable chronyd.service
```

Linux 上的配置：

```
vim /etc/crontab   #添加以下内容
# 172.16.100.254 为服务端的地址
```

![|inline](https://i.328888.xyz/2023/03/17/LEEed.png)

用date查看时间方可进行检测

![|inline](https://i.328888.xyz/2023/03/17/LEeab.png)

![|inline](https://i.328888.xyz/2023/03/17/LE6kq.png)

## DNS

### Liunx1为主DNS

```
yum install -y bind bind-utils #安装dns软件包
vim /etc/named.conf #修改主配置文件
```

```
options {
	listen-on port 53 { any; }; #53端口在所有网段进行侦
	allow-query     { any; }; #允许所有网段查询
```

```
vim /etc/named.rfc1912.zones #在zone文件里添加正反向区域
```

```
zone "skills.com" IN { #正向查找区域zone文件
        type master; #类型为主人
        file "zx1"; #文件名为zx1
        allow-update { none; };
};

zone "70.10.10.in-addr.arpa" IN { #反向查找区域zone文件
        type master; #类型为主人
        file "fx1";#文件名为fx1
        allow-update { none; };
};
```

```
cd /var/named/ #进入这个目录
cp -a named.localhost zx1 #将这个文件所有内容属性复制到zx1
vim zx1#编辑zx1（正向查找）
```

```
$TTL 1D
@       IN SOA  cs1 rname.invalid. ( #SOA记录
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      cs1.skills.com.#NS记录
        NS      cs2.skills.com.
        A       127.0.0.1
        AAAA    ::1
        MX      5       cs2 #MX记录
cs1     A       10.10.70.11 #A记录
cs2     A       10.10.70.12
cs3     A       10.10.70.13
cs4     A       10.10.70.14
cs5     A       10.10.70.15
cs6     A       10.10.70.16
cs7     A       10.10.70.17
tomcat  CNAME   cs1
```

### Linux2为备用DNS

```
yum install -y bind bind-utils #安装dns软件包
vim /etc/named.conf #修改主配置文件
```

```
@       IN SOA  cs1.skills.com. rname.invalid. (
                                        0       ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      cs1.skills.com.
        NS      cs2.skills.com.
1       PTR     cs1.skills.com. #PTR记录
2       PTR     cs2.skills.com.
3       PTR     cs3.skills.com.
4       PTR     cs4.skills.com.
5       PTR     cs5.skills.com.
6       PTR     cs6.skills.com.
7       PTR     cs7.skills.com.
```

## SSH

### 在主机上生成rsa密钥对

```
ssh-keygen -t rsa
```


### 将生成的公钥互相传递

```
ssh-copy-id root@ip
```


## CA 服务器

### 手动创建必须的目录和文件

```
mkdir -p /etc/pki/CA
cd /etc/pki/CA
mkdir certs,crl,newcerts,private
touch index.txt #证书的索引数据库文件
echo 01 >serial #存放每个证书的编号文件
```


### 生成CA的私钥

```
(umask 077;openssl genrsa -out private/cakey.pem 2048)
```


### 生成自签名的CA证书（公钥）

```
openssl req -new -x509 -key private/cakey.pem -out cacert.pem -days 7300
```

Country Name (2 letter code) [XX]:CN

State or Province Name (full name) []:Beijing

Locality Name (eg, city) [Default City]:Beijing

Organization Name (eg, company) [Default Company Ltd]:skills

Organizational Unit Name (eg, section) []:system

Common Name (eg, your name or your server's hostname) []:cs1.skills.com

Email Address :

### 验证命令

```
[root@cs1 CA]# date
[root@cs1 CA]# openssl x509 -in /etc/pki/CA/cacert.pem -noout -subject -issuer -enddate
```

