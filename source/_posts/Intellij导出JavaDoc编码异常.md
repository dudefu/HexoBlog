---
title: Intellij导出JavaDoc编码异常
categories: Java
tags: Intellij
abbrlink: 9e806fd6
date: 2017-12-12 17:20:34
---
Intellij IDEA 导出JavaDoc时编码异常之解决方案。

javac编译提示错误需要为 class、interface 或 enum 
HelloWorld.java:1: 需要为 class、interface 或 enum
锘缝ublic class HelloWorld{
^
D:\IDE-workspace\src\com\dudefu\www\Test2.java:1: 错误: 非法字符: '\ufeff'
?package com.dudefu.www;
<!----more----->
^
D:\IDE-workspace\src\com\dudefu\www\Test2.java:1: 错误: 需要class, interface或enum
?package com.dudefu.www;
         ^
3 错误
## 原因
这个错误出现的原因主要是在中文操作系统中，使用一贯的“javac HelloWorld.java”方式编译UTF-8（带BOM）编码的.java源文件，在没有指定编码参数（encoding）的情况下，默认是使用GBK编码。当编译器用GBK编码来编译UTF-8文件时，就会把UTF-8（带BOM）编码文件的文件头的占3个字节的头信息，按照GBK中汉字占两个字节、英文占1个字节的特性解码成了“乱码”的两个汉字。这个源文件应该是用记事本另存存为UTF-8编码造成的。
 对于非GBK及其子集编码（GB2312）的正确的源文件，编译方式为“javac -encoding "UTF-8" HelloWord.java”，这样代码错误的指定代码里就不会出现乱码的中文。
 但是依然会有错误，提示“HelloWorld.java:1: 非法字符: \65279。  
这是因为.java对于UTF-8编码，只识别UTF-8（不带BOM）那种。而记事本只支持保存文件为带签名的UTF-8，那有没有办法解决呢？
当然是有的，那就是使用EmEditor、EditPlus、UltraEdit或Notepad++之类的工具另存为UTF（不带BOM）（区别于带UTF + BOM）的编码文件。这时候使用“javac -encoding "UTF-8" HelloWorld.java”，就没有上述编码问题了。
也许有人会说，“我干脆都用GBK不就行了吗，为什么还要用UTF-8呢？”
这是因为UTF-8支持世界多种语言的文字，被世界多数国家接受，是国际通用编码，也是Java推荐使用的编码。Java集成开发环境Eclipse中默认编码就是UTF-8。如果使用GBK，尤其是做网站，在非汉语国家，将无法正常浏览。在信息化时代，国际交往日益频繁；做软件和网站，不能只着眼当前，也要为日后维护做优化、降低维护成本。
