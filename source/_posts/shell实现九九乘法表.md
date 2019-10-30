---
title: shell实现九九乘法表
categories: Linux
tags: shell
abbrlink: f3a87322
date: 2018-02-06 16:34:59
---
```shell
for (( i=1;i<=9;i++ ))
do
        for (( j=1;j<=i;j++ ))
        do
            echo -n "$j*$i=$[$j*$i] "
        done
        echo -e '\n'
done
```