---
title: RDD转DataFrame的一道面试题
categories: 大数据
tags:
  - RDD
  - 面试题
abbrlink: e35d961c
date: 2018-05-04 20:14:05
---
## 题目
现在在我们HDFS文件系统上面 存了一个文件，该文件格式是 .txt文件格式，要求把这个文件格式转换成为parquet文件格式 :
解题思路:   
1）先读取文件生成一个RDD
2）把RDD转换成为一个DataFrame，RDD[Person].toDF
3) 写数据，指定文件格式就可以了！！
<!---more--->
代码实现 :
```Scala
val conf = new SparkConf().setMaster("local").setAppName("DataFrameReflection")
    val sc = new SparkContext(conf)
    val sqlContext = new SQLContext(sc)

    import sqlContext.implicits._
    val peopelRDD: RDD[People] = sc.textFile("hdfs://hadoop01:9000/resources/people.txt")
      .map(line => People(line.split(",")(0),line.split(",")(1).trim.toInt))

    val df = peopelRDD.toDF()
    df.write.format("parquet").save("hdfs://hadoop01:9000/test/")
```