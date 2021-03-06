---
title: kafka基本操作
categories: 大数据
tags: KafKa
abbrlink: 51666
date: 2019-07-26 09:57:03
---
## kafka基本操作：
## 查看topic主题：
kafka-topics.sh --list --zookeeper node1:2181,node2:2181,node3:2181

## 查看分区：
kafka-topics.sh --zookeeper node2:2181,node3:2181,node4:2181  --describe --topic  MotorVehicle
kafka-topics.sh --zookeeper node2:2181,node3:2181,node4:2181  --describe --topic  WifiRecord
kafka-topics.sh --zookeeper node2:2181,node3:2181,node4:2181  --describe --topic  ImsiRecord
<!-- more -->

## 创建topic主题：
kafka-topics.sh --create --zookeeper node1:2181,node2:2181,node3:2181 --replication-factor 1 --partitions 1 --topic MotorImsi

## 删除主题：
kafka-topics.sh --delete --zookeeper node1:2181,node2:2181,node3:2181 --topic Test004

## 开启生产者：（注意kafka所在节点）目前都是单节点
kafka-console-producer.sh --broker-list node3:6667 --topic MotorWifi

## 开启消费者：
kafka-console-consumer.sh --bootstrap-server node1:6667,node2:6667,node3:6667 --topic Test003 --from-beginning


## kafka中默认消息的保留时间是7天，若想更改，需在配置文件
server.properties里更改选项：
log.retention.hours=168
但是有的时候我们需要对某一个主题的消息存留的时间进行变更，而不影响其他主题。

## 可以使用命令：
kafka-configs.sh –zookeeper localhost:2181 –entity-type topics –entity-name topicName –alter –add-config log.retention.hours=120
使得主题的留存时间保存为5天

## 如果报错的话，可以将时间单位更改成毫秒：
kafka-configs.sh –zookeeper localhost:2181 –entity-type topics –entity-name test –alter –add-config retention.ms=43200000



## 将jar包放入后台运行：
nohup java -jar  xinyi_kafka_consumer-0.0.1-SNAPSHOT-imsi.jar >> xinyi_kafka_consumer-0.0.1-SNAPSHOT-imsi.out 2>&1 &

nohup java -jar  xinfo-spark-scheduler.jar  >> xinfo-spark-scheduler.out 2>&1 &

nohup java -jar  xinyi_kafka_consumer_video-0.0.1-SNAPSHOT.jar >> xinyi_kafka_consumer_video-0.0.1-SNAPSHOT.out 2>&1 &