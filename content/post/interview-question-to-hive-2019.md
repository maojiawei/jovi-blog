---
title: "2019年Hive面试问题汇总"
date: 2019-08-04T18:43:20+08:00
draft: false
tags: ["hive"]
categories: "bigdata"
toc: false
---
## Apache Hive—简介
&emsp;Apache Hive是一个基于Hadoop的数据仓库系统，它被用来分析处理结构化或半结构化的数据。它提供了一种机制，将计算逻辑投影至数据中。它还提供了一种HQL语言，这种语言类似于SQL的语法。在Hive的内部，这些语法可以通过Hive的编辑器转换成Map Reduce。

## Apache Hive 相关问题
&emsp;以下是经过与行业专家深入研究和讨论后最常见的Apache Hive面试问题的综合列表。


1.Hive与Hbase的区别  

|HBase|Hive|
|----|----|
|1.HBase构建与HDFS中|1.这是一种数据仓库|
|2.HBase在它的数据库中是实时的|2.Hive查询是基于Map Reduce Job|
|3.HBase查询大表单行延迟低|3.Hive查询大表单行延迟高|
|4.HBase提供对数据的随机访问|4.HBase提供对数据的随机访问|

2.Hive适用于哪种类型的应用  
Hive支持多种客户端，例如:

- JAVA
- PHP
- Python
- C++
- Ruby

3.Hive的表数据保存在哪里?  
Hive的表数据默认存放在HDFS的目录—/user/hive/warehouse。一个这个可以通过`hive-site.xml`中的`hive.metastore.warehouse.dir`参数进行修改。

4.Hive的Metastore保存在哪里?  
Hive中的Metastore使用RDBMS和名为Data Nucleus的开源ORM（对象关系模型）层存储元数据信息，该层将对象表示转换为关系模式，反之亦然。

5.Hive为何不将Metastore保存至HDFS中?  
Hive存储元数据(metadata)信息通过RDBMS(而不是HDFS)在metastore。这个原因是因为RDBMS是低延迟的，而HDFS的读写都是非常耗时的。

6.本地和远程Metastore的区别?  
本地Metastore:  
在本地Metastore配置中，Metastore和Hive运行在同一个JVM中，并连接到单独的JVM运行的数据库。这种模式可以在同一台计算机中运行，也可以在远程计算机上运行。  
远程Metastore:  
在远程Metastore配置中，Metastore和Hive运行在两个不同的JVM中。其他进程使用Thrift Network API与Metastore服务器通信。 在这种情况下，可以拥有一个或多个Metastore服务器以提供更多可用性。  

7.Apache Hive通过Metastore提供的默认数据库是什么?  
Hive默认提供有本地磁盘的Metastore的侵入式Derby数据。这也被称为侵入式Metastore配置。  

8.场景:  
假设Apache Hive已经通过默认配置的Metastore安装在我的Hadoop集群中。然后，如果我们有许多客户端试图在同一时间连接Hive，那会发生什么事情？  
默认Metastore允许一个Hive的session在同一时间开启访问Metastore。然而，如果许多客户端在同一时间访问，那可能会产生异常。一个需要有单点的metastore，例如:本地或远程metastore，即可通过多个客户端访问。  
以下步骤可以通过配置Mysql数据库作为本地Metastore:  

- hive-site.xml中可以修改以下配置:  
&emsp;&emsp;`javax.jdo.option.ConnectionURL`参数可以设置成`jdbc:mysql://host/dbname?createDataba  seIfNotExist=true.`。  
&emsp;&emsp;`javax.jdo.option.ConnectionDriverName`参数可以设置成`com.mysql.jdbc.Driver`。  
&emsp;&emsp;需要设置用户名密码:  
&emsp;&emsp;&emsp;`javax.jdo.option.ConnectionUserName`可以设置成指定的用户。  
&emsp;&emsp;&emsp;`javax.jdo.option.ConnectionPassword `可以设置成指定的密码。  
&emsp;&emsp;JDBC驱动中的MYSQL的JAR文件必须在Hive的classpath。例如:jar文件需要放置在Hive的lib目录中。  
&emsp;&emsp;现在，重启Hive，就可以自动连接mysql数据库，运行在一个单节点的metastore中。  