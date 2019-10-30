---
title: Java实现九九乘法表
categories: Java
tags: JavaSE
abbrlink: f1589310
date: 2017-11-30 16:35:35
---
关键难点：内循环的条件判断
```Java
public static void main(String[] args) {
        //九九乘法表
        for(int i = 1 ;i<=9;i++){
            for(int j = 1;j<=i;j++){
                System.out.print(j+"*"+i+"="+j*i+" ");
            }
            System.out.println("\n");
        }
    }
```
<!---more--->
输出结果：
![](http://ou3xxg3hg.bkt.clouddn.com/九九乘法表.jpg)