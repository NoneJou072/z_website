+++
title = 'Git'
date = 2024-01-29T14:53:51+08:00
draft = true
+++

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
