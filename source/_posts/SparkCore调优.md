---
title: SparkCore调优
categories: 大数据
tags: Spark
abbrlink: 6663ecc
date: 2018-04-25 15:36:43
---
## 开发角度

1. 原则一：避免创建重复的RDD
2. 原则二：尽可能用同一个RDD
3. 原则三：对多次使用的RDD进行持久化
   如何选择一种最合适的持久化策略
   - MEMORY_ONLY
   - MEMORY_ONLY_SER
   - MEMORY_AND_DISK_SER
   - 不考虑：DISK_ONLY和_2后缀
<!---more--->
4. 原则四：尽量避免使用shuffle类算子
   - 能不用就不用
   - 能不能用非shuffle类的算子去替代非shuffle类的join -》 map操作替代
5. 原则五：使用map-side预聚合的shuffle操作：groupBykey 和 reduceBykey
6. 原则六：使用高性能的算子：
   - 使用reduceBykey//aggregateBykey替代groupBykey
   - 使用mapPartitions替代普通map
   - 使用foreachPartitions替代foreach
   - 使用filter之后进行coalesce操作
   - 使用repartitionAndSortWithhinPartitions替代repartition与sort类操作
7. 原则七：广播大变量
8. 原则八：使用Kryo优化序列化性能
    - conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
    - conf.registerKryoClasses(Array(classOf[MyClass1], classOf[MyClass2]))
9. 优化数据结构
10. 原则十：Data Locality
    - PROCESS_LOCAL data is in the same JVM as the running code. This is the best locality possible
    - NODE_LOCAL data is on the same node. Examples might be in HDFS on the same node, or in another executor on the same node. This is a little slower than PROCESS_LOCAL because the data has to travel between processes
    - NO_PREF data is accessed equally quickly from anywhere and has no locality preference
    - RACK_LOCAL data is on the same rack of servers. Data is on a different server on the same rack so needs to be sent over the network, typically through a single switch
    - ANY data is elsewhere on the network and not in the same rack
    - 默认值-spark.locality.wait-3s
    - spark.locality.wait.process-建议60s
    - park.locality.wait.node-建议30s
    - spark.locality.wait.rack-建议20s

## 数据倾斜（面试的重点） [美团技术博客](https://tech.meituan.com/spark-tuning-pro.html)

### 数据倾斜：
    
数据倾斜发生时的现象：
- 绝大多数task执行的都非常快，但个别task执行极慢
- 原本能够正常执行的Spark作业，某天突然报出OOM（内存溢出）异常
数据倾斜发生的最根本原因
如何定位导致数据倾斜的代码：
- shuffle（找代码里面发生shuffle的算子）
- stage划分 界面观察就可以定位到是哪个算子导致的数据倾斜
如何定位到哪个key导致的数据倾斜：
- 方式一： countBykey 有可能出来结果，但是会遇到数据倾斜
- 方式二：sample countBykey

### 方案解决：

解决方案一：使用Hive ETL预处理数据
- 方案实现思路----Hive实现预处理
- 方案实现原理----数据倾斜的发生提前到了Hive ETL中，避免Spark程序发生数据倾斜而已
- 方案优点-----实现起来简单便捷，效果还非常好，完全规避掉了数据倾斜，Spark作业的性能会大幅度提升
- 方案缺点-----治标不治本，Hive ETL中还是会发生数据倾斜

解决方案二：过滤少量导致倾斜的key
- 方案实现原理-将导致数据倾斜的key给过滤掉之后，这些key就不会参与计算了，自然不可能产生数据倾斜。
- 方案优点----实现简单，而且效果也很好，可以完全规避掉数据倾斜
- 方案缺点----key对于我们来说，没有实际意义才行

解决方案三：提高shuffle操作的并行度（没多大用）
- 方案优点----实现起来比较简单，可以有效缓解和减轻数据倾斜的影响
- 方案缺点----只是缓解了数据倾斜而已，没有彻底根除问题，根据实践经验来看，其效果有限。

解决方案四：两阶段聚合（局部聚合+全局聚合）
- 方案实现原理----将原本相同的key通过附加随机前缀的方式，变成多个不同的key，就可以让原本被一个task处理的数据分散到多个task上去做局部聚合，进而解决单个task处理数据量过多的问题。接着去除掉随机前缀，再次进行全局聚合，就可以得到最终的结果。
- 方案优点----对于聚合类的shuffle操作导致的数据倾斜，效果是非常不错的。通常都可以解决掉数据倾斜，或者至少是大幅度缓解数据倾斜，将Spark作业的性能提升数倍以上。
- 方案缺点----仅仅适用于聚合类的shuffle操作----groupBykey，join类的shuffle操作