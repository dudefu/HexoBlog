---
title: git配置多个SSH-Key
categories: 系统
tags: github
abbrlink: 27450
date: 2019-08-16 15:55:12
---
# 1、生成一个公司用的SSH-Key     
```shell
$ ssh-keygen -t rsa -C "youremail@yourcompany.com” -f ~/.ssh/id-rsa
```
在~/.ssh/目录会生成id-rsa和id-rsa.pub私钥和公钥。 我们将id-rsa.pub中的内容粘帖到公司gitlab服务器的SSH-key的配置中。
<!--more-->
# 2、 生成一个github用的SSH-Key
```shell
$ ssh-keygen -t rsa -C "youremail@your.com” -f ~/.ssh/github-rsa
```
在~/.ssh/目录会生成github-rsa和github-rsa.pub私钥和公钥。 我们将github-rsa.pub中的内容粘帖到github服务器的SSH-key的配置中。
# 3、添加私钥
```shell
$ ssh-add ~/.ssh/id_rsa $ ssh-add ~/.ssh/github_rsa
```
如果执行ssh-add时提示"Could not open a connection to your authentication agent"，可以现执行命令：
```shell
$ ssh-agent bash
```
然后再运行ssh-add命令。
可以通过 ssh-add -l 来确私钥列表
```shell
$ ssh-add -l
```
可以通过 ssh-add -D 来清空私钥列表
```shell
$ ssh-add -D
```
# 4、修改配置文件
在 ~/.ssh 目录下新建一个config文件
```shell
touch config
```
添加内容：
```shell
# gitlab
Host gitlab.com
    HostName gitlab.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
# github
Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/github_rsa
```
# 5、测试
```shell
$ ssh -T git@github.com
```
输出
Hi stefzhlg! You've successfully authenticated, but GitHub does not provide shell access.
