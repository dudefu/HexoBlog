---
title: JavaWeb开发
categories: Java
tags: JavaWeb
abbrlink: 6b2f5538
date: 2017-08-17 10:33:54
---
1 JSP3个编译指令 : page,include,taglib
2 JSP动作指令7个：
jsp:forward ： 执行页面转向，将请求的处理转发到下一个页面
jsp:aram : 用于传递参数，必须与其他支持参数的标签一起使用
jsp:include ： 用于动态引入一个JSP页面
jsp:plugin : 用于下载JavaBean 或 Applet到客户端执行
jsp:useBean : 创建一个JavaBean的实例
jsp:setProperty : 设置JavaBean 实例的属性值
jsp:getProperty : 输出JavaBean 实例的属性值
3 include指令
include指令是一个动态include指令，也用于包含某个页面，它不会导入被include页面的编译指令，仅仅将被导入页面的body内容插入本页面
forward动作指令和include指令的区别：执行forward时，被forward的页面将完全代替原有页面；而执行include时，被include的页面只是插入原有页面。简而言之：forward拿目标页面代替原有页面，而include则拿目标页面插入原有页面。
![](http://ou3xxg3hg.bkt.clouddn.com/JSP9%E5%A4%A7%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1.png)
