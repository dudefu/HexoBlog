---
title: Java基础编程题
categories: Java
tags: JavaSE
abbrlink: ab77ed1d
date: 2017-12-16 09:11:23
---
1. 把一个数组arr[n]进行反转
```Java
//方法一，思路：利用for循环，只循环n/2-1次，在同一个数组里进行值的交换
 int[] arr = {1,2,3,4,5,6,7,8,9};
        for (int i = 0,j = arr.length-1; i < arr.length; i++,j--) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp ;
            if(i==3)
                break;
        }
        System.out.println(Arrays.toString(arr));
         <!---more--->
//方法二，思路：重新new一个数组，将原数组逆向赋值给新数组
int[] arrTemp = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            arrTemp[i] = arr[arr.length-i-1];
        }
        arr = arrTemp ;
        System.out.println(Arrays.toString(arr));
//方法三，思路：通过Collections.reverse(list);
 ArrayList<Integer> list = new ArrayList<Integer>();
        for (int i = 0; i < arr.length; i++) {
            list.add(arr[i]);
        }
        Collections.reverse(list);
        System.out.println( list);

```
       