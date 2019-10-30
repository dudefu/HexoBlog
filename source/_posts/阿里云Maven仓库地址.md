---
title: 阿里云Maven仓库地址
categories: 有料
tags: Maven
abbrlink: c0f570a1
date: 2017-08-05 02:09:23
---

## 阿里云Maven仓库地址
<http://maven.aliyun.com/nexus/#view-repositories;public~browsestorage>
在maven的settings.xml 文件里配置mirrors的子节点，添加如下mirror
```
<mirror>
       <id>nexus-aliyun</id>
       <mirrorOf>*</mirrorOf>
       <name>Nexus aliyun</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror> 
```
<!-- more -->