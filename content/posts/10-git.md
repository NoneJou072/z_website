---
title: 常用 git 命令 
description: 常用 git 命令 
toc: true
authors:
  - Haoran Zhou
tags: ["git"]
categories:
series:
date: '2023-12-21T13:11:22+08:00'
lastmod: '2023-12-23T13:11:22+08:00'
featuredImage:
draft: false
---

## 配置 github 的 ssh 公钥
在新的主机上使用 github 时为了避免输入用户名和密码，可以在 github 账号下配置 ssh 的公钥。
具体步骤如下：
1. 第一步：检查本地主机是否已经存在ssh key
```bash
cd ~/.ssh
ls
//看是否存在 id_rsa 和 id_rsa.pub文件，如果存在，说明已经有SSH Key
```
如果存在，直接跳到第三步
2. 第二步：生成ssh key
如果不存在ssh key，使用如下命令生成
```bash
ssh-keygen -t rsa -C "xxx@xxx.com"
//执行后一直回车即可
```
3. 第三步：获取ssh key公钥内容（id_rsa.pub）
```bash
cd ~/.ssh
cat id_rsa.pub
```
4. 第四步：Github账号上添加公钥
GITHUB -> SETTINGS -> SSH AND GPG KEYS -> NEW SSH KEY
将公钥内容复制粘贴进去即可。
5. 第五步：验证是否设置成功
```bash
ssh -T git@github.com
```

## git push代码到远程新分支
获取远程代码修改后,想要push到远端与原来不同的新分支，可以使用下面的命令实现：
```bash
git push origin 本地分支:远端希望创建的分支
```
例如git下来的分支为master
```bash
git branch
>>> *master
git push origin master:my_remote_new_branch
#远端即可创建新的分支my_remote_new_branch,提交本地修改
```

## 使更改的.gitignore文件生效
运行以下命令，清除 Git 缓存中的所有文件，以确保 Git 会重新读取 .gitignore 文件
```
git rm -r --cached .
```
这个命令会将 Git 缓存中的所有文件标记为需要删除。

运行以下命令，将未被 Git 跟踪的文件添加回到 Git 仓库中：
```
git add .
```

运行以下命令，提交更改
```
git commit -m "Update .gitignore"
```
这个命令将提交你的更改并将它们添加到 Git 历史记录中。

## submodule
常见场景：当项目依赖并跟踪一个开源的第三方库时，将第三方库设置为submodule

参考文章 [Git中submodule的使用
](https://zhuanlan.zhihu.com/p/87053283)
### 创建 submodule

### 获取 submodule
如果希望子模块代码也获取到，一种方式是在克隆主项目的时候带上参数 `--recurse-submodules`，这样会递归地将项目中所有子模块的代码拉取。

对于已经部署的项目, 可以在当前主项目中执行：
```bash
git submodule init
git submodule update
```
则会根据主项目的配置信息，拉取更新子模块中的代码。
