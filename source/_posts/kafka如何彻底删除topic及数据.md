---
title: kafka如何彻底删除topic及数据
categories: 大数据
tags: Kafka
abbrlink: 28587
date: 2019-07-26 09:56:39
---
## 前言：
删除kafka topic及其数据，严格来说并不是很难的操作。但是，往往给kafka 使用者带来诸多问题。项目组之前接触过多个开发者，发现都会偶然出现无法彻底删除kafka的情况。本文总结多个删除kafka topic的应用场景，总结一套删除kafka topic的标准操作方法。
## step1：
如果需要被删除topic 此时正在被程序 produce和consume，则这些生产和消费程序需要停止。
因为如果有程序正在生产或者消费该topic，则该topic的offset信息一致会在broker更新。调用kafka delete命令则无法删除该topic。
同时，需要设置 auto.create.topics.enable = false，默认设置为true。如果设置为true，则produce或者fetch 不存在的topic也会自动创建这个topic。这样会给删除topic带来很多意向不到的问题。
所以，这一步很重要，必须设置auto.create.topics.enable = false，并认真把生产和消费程序彻底全部停止。
<!-- more -->
## step2：
server.properties 设置 delete.topic.enable=true
如果没有设置 delete.topic.enable=true，则调用kafka 的delete命令无法真正将topic删除，而是显示（marked for deletion）
## step3：
调用命令删除topic：
./bin/kafka-topics --delete --zookeeper 【zookeeper server:port】 --topic 【topic name】
## step4：
删除kafka存储目录（server.properties文件log.dirs配置，默认为"/data/kafka-logs"）相关topic的数据目录。
注意：如果kafka 有多个 broker，且每个broker 配置了多个数据盘（比如 /data/kafka-logs,/data1/kafka-logs ...），且topic也有多个分区和replica，则需要对所有broker的所有数据盘进行扫描，删除该topic的所有分区数据。
 
一般而言，经过上面4步就可以正常删除掉topic和topic的数据。但是，如果经过上面四步，还是无法正常删除topic，则需要对kafka在zookeeer的存储信息进行删除。具体操作如下：
（注意：以下步骤里面，kafka在zk里面的节点信息是采用默认值，如果你的系统修改过kafka在zk里面的节点信息，则需要根据系统的实际情况找到准确位置进行操作）
## step5：
找一台部署了zk的服务器，使用命令：
bin/zkCli.sh -server 【zookeeper server:port】
登录到zk shell，然后找到topic所在的目录：ls /brokers/topics，找到要删除的topic，然后执行命令：
rmr /brokers/topics/【topic name】
即可，此时topic被彻底删除。
如果topic 是被标记为 marked for deletion，则通过命令 ls /admin/delete_topics，找到要删除的topic，然后执行命令：
rmr /admin/delete_topics/【topic name】
备注：
网络上很多其它文章还说明，需要删除topic在zk上面的消费节点记录、配置节点记录，比如：
rmr /consumers/【consumer-group】
rmr /config/topics/【topic name】
其实正常情况是不需要进行这两个操作的，如果需要，那都是由于操作不当导致的。比如step1停止生产和消费程序没有做，step2没有正确配置。也就是说，正常情况下严格按照step1 -- step5 的步骤，是一定能够正常删除topic的。
step6：
完成之后，调用命令：
./bin/kafka-topics.sh --list --zookeeper 【zookeeper server:port】
查看现在kafka的topic信息。正常情况下删除的topic就不会再显示。
但是，如果还能够查询到删除的topic，则重启zk和kafka即可。