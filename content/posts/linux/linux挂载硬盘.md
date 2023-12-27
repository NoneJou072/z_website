---
title: linux挂载新硬盘
description: 挂载硬盘。
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

## 挂载硬盘

查看磁盘名称等信息
```
lsblk 
```
可以看到我要挂载硬盘的名称是`/dev/sda1`

挂载分区
```
mkdir /test
mount /dev/sda1 /test
lsblk
```
这样就挂载成功，但是重启系统就需要重新挂载，这个时候我们需要开机自动挂载

参考: [Ubuntu 磁盘挂载——开机自动挂载](https://blog.csdn.net/qq_35451572/article/details/79541106)

查询挂载硬盘UUID
```
sudo blkid /dev/sda1
```
返回信息
```
/dev/sda1: LABEL="data" BLOCK_SIZE="512" UUID="0AA6352E62EA7D6B" TYPE="ntfs" PARTUUID="19a435b2-37b3-4229-9728-29c163e54b74"
```
可以看到`UUID="0AA6352E62EA7D6B"`, `TYPE="ntfs" `

修改`/etc/fstab`文件
在文档末尾添加新磁盘信息
```
UUID=0AA6352E62EA7D6B /media/4T ntfs defaults  0  2
```

* 第一个数字：0表示开机不检查磁盘，1表示开机检查磁盘；
* 第二个数字：0表示交换分区，1代表启动分区（Linux），2表示普通分区
* 我挂载的分区是在WIn系统下创建的分区，磁盘格式为ntfs
