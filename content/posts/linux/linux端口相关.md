---
title: linux端口通信相关
description: linux端口相关操作。
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-12-14T13:11:22+08:00'
lastmod: '2023-12-14T13:11:22+08:00'
featuredImage:
draft: false
---

## 端口信息查看
查看当前所有tcp端口情况
```bash
netstat -ntlp   
```

linux查看所有2541端口占用情况
```bash
netstat -tunlp | grep 2541

输出：
tcp        0      0 192.168.50.94:2541      0.0.0.0:*               LISTEN      3882497/python3
```
`3882497` 是该IP地址对应的的进程PID，通过该进程号可以很容易的找到该进程的相关的所有信息，只需要通过下面的这个命令：
```bash
cd /proc/3882497
```

简单说明一下上面的 -tunlp这几个参数的意义

* -t (tcp) 仅显示tcp相关选项
* -u (udp)仅显示udp相关选项
* -n 拒绝显示别名，能显示数字的全部转化为数字
* -l 仅列出在Listen(监听)的服务状态
* -p 显示建立相关链接的程序名

当 python 程序在异常退出且占用了一个端口时，可以通过上面的方法定位到相应进程，然后kill掉
```bash
kill -9 3882497
```


## 使用 Python socket 库在两个机器间TCP通讯
使用 Python socket 库在两个机器间通讯时，客户端和服务端的 ip 都要指向服务端，并保持端口号一致。

可以测试两台主机之间是否能相互ping通。
运行时，要首先启动服务端，再启动客户端。


## 端口开启/关闭
两台机器进行socket通信时，可能在连接时出现错误： 
```bash
Connect error: No route to host(errno:113) 
```
出错原因：server端的防火墙设置了过滤规则

### 一种解决办法：使用iptables关闭server端的防火墙

1.暂时关闭
```bash
sudo service iptables stop
```
2.打开
```bash
sudo service iptables start
```
3.永久打开和关闭
```bash
sudo chkconfig iptables on/off
```
但是可能在执行时报错 
```bash
Failed to start iptables.service: Unit iptables.service failed to load: No such file or directory.
或 Failed to stop iptables.service: Unit iptables.service not loaded.
```
原因是在 CentOS 7 或 RHEL 7 或 Fedora 中防火墙由 firewalld 来管理，

为了使用`iptables`，执行命令：
```bash
systemctl stop firewalld
systemctl mask firewalld
```
安装`iptables-services`：
```bash
yum install iptables-services
```
设置开机启动：
```bash
systemctl enable iptables
```
iptables 相关操作命令：
```bash
systemctl stop iptables
systemctl start iptables
systemctl restart iptables
systemctl reload iptables
```
保存设置：
```bash
service iptables save
```
OK，再试一下应该就好使了

开放某个端口 在/etc/sysconfig/iptables里添加
```
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
```

### 一种解决办法：使用firewalld关闭server端的防火墙
如果要添加范围例外端口 如 1000-2000
语法命令如下：启用区域端口和协议组合
```bash
firewall-cmd [--zone=<zone>] --add-port=<port>[-<port>]/<protocol> [--timeout=<seconds>]
```
此举将启用端口和协议的组合。端口可以是一个单独的端口 <port> 或者是一个端口范围 <port>-<port> 。协议可以是 tcp 或 udp。
实际命令如下：
```bash
添加

firewall-cmd --zone=public --add-port=80/tcp --permanent （--permanent永久生效，没有此参数重启后失效）

firewall-cmd --zone=public --add-port=1000-2000/tcp --permanent 

重新载入
firewall-cmd --reload
查看
firewall-cmd --zone=public --query-port=80/tcp
删除
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```
