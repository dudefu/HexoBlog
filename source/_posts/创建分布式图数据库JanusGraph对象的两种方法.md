---
title: 创建分布式图数据库JanusGraph对象的两种方法
categories: 图数据库
tags: JanusGraph
abbrlink: 53657
date: 2019-07-04 15:18:35
---
JanusGraph 是一个分布式图数据库，相对于neo4j可进行横向扩展，且存储和图引擎分离，架构优美，本文将介绍JanusGraph的两种创建方式。
<!---more--->
1、添加Maven依赖 
```xml
<dependency>
    <groupId>org.janusgraph</groupId>
    <artifactId>janusgraph-core</artifactId>
    <version>0.2.0</version>
</dependency>
     
<dependency>
    <groupId>org.janusgraph</groupId>
    <artifactId>janusgraph-cassandra</artifactId>
    <version>0.2.0</version>
</dependency>
     
<dependency>
    <groupId>org.janusgraph</groupId>
    <artifactId>janusgraph-es</artifactId>
    <version>0.2.0</version>
</dependency>
```
有以下两种方式构建JanusGraph对象
1、通过配置文件构建图对象 
```Java
JanusGraph graph = JanusGraphFactory.open("janusgraph/conf/janusgraph-cassandra-es.properties");
graph.close();
```
2、通过Configuration构建图对象 
```Java
import org.apache.commons.configuration.BaseConfiguration;
import org.apache.tinkerpop.gremlin.process.traversal.dsl.graph.GraphTraversalSource;
import org.janusgraph.core.JanusGraph;
import org.janusgraph.core.JanusGraphFactory;
 
public class Test {
    public static void main(String[] args) {
        BaseConfiguration config = new BaseConfiguration();

        ////////////使用内存作为存储端
        //config.setProperty("storage.backend", "inmemory");
        //////////使用cassandra+es作为存储端
        config.setProperty("storage.backend", "cassandrathrift");
        config.setProperty("storage.cassandra.keyspace", "janus");
        config.setProperty("storage.hostname", "127.0.0.1");
        config.setProperty("index.search.backend", "elasticsearch");
        config.setProperty("index.search.hostname", "127.0.0.1");
 
        config.setProperty("cache.db-cache", "true");
        config.setProperty("cache.db-cache-time", "300000");
        config.setProperty("cache.db-cache-size", "0.5");
        ;

        JanusGraph graph = JanusGraphFactory.open(config);
        GraphTraversalSource g = graph.traversal();
 
        //其它逻辑代码
 
        g.tx().rollback();
        graph.close();
 
    }
}
```