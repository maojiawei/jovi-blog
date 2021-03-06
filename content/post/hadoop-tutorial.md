---
title: "Hadoop入门"
date: 2019-01-03T22:25:56+08:00
draft: false
tags: ["hadoop"]
categories: "bigdata"
toc: false
---
&emsp;如果正在学习Hadoop，这是一篇最好的入门文章。在这篇关于Hadoop入门的博客中，你将会在几个简单的阶段中从hadoop的基础到高阶概念。另外，你也可以看下方的视频，该视频会由我们的Hadoop专家通过实际的例子来讲解Hadoop的基本概念。  
&emsp;在这篇博客中，我们将从以下几个主题出发:  

* 如何开始
* 什么是大数据
* 大数据与Hadoop:餐厅类比
* 什么是Hadoop
* Hadoop解决方案
* Hadoop特性
* Hadoop核心组件
* Last.fm案例研究

## 1.0 如何开始
&emsp;在开始进入我们关于Hadoop的主题之前，我们先来一个有趣的故事来说明Hadoop是如何实现的，以及它今年来为什么如此受欢迎。这都起源于两个人，Mike Cafarella和Doug Cutting。他们正致力于建立一个可以对一亿个网页进行索引的搜索引擎系统。在他们的研究之后，他们发现这个系统将在硬件方面花费$500,000以及每月$30,000的维护成本，这个话费是十分昂贵的。而且，他们很快意识到，他们的架构将不能支持数以亿计的网页。  
&emsp;他们偶然发现了一篇论文，这篇论文布于2003年，主要介绍谷歌的分布式文件系统架构(GFS)，这个系统已用于谷歌的生产环境。现在这篇关于GFS的论文被证明是他们需要的。很快地，他们意识到这能解决他们对于大文件的存储，这些文件由网络爬虫以及索引处理产生。在2004年，谷歌发表了一篇新的论文，向全世界介绍关于MapReduce的处理框架。最终，这两篇文章成为了Hadoop这个框架的基础。Doug在Hadoop的开发过程中是这样描述谷歌的:谷歌将在未来持续向世界传递信息。  
&emsp;现在，你知道Hadoop是多么强大了吧！现在，在介绍Hadoop之前，让我们讨论下大数据，这将带领我们进入Hadoop的开发。

## 2.0 什么是大数据
&emsp;你有没有想过，技术是如何演变来实现日益增长的新需求?例如，最早我们使用座机，而现在我们都改用智能手机。相似地，90后有多少人记得当时用地如此广泛的软盘驱动器。如今，软盘已经被硬盘取代，因为这些软盘在存储的容量和速度上是非常差的。然而，这也使软盘在处理如今大量数据的效率是不足的。事实上，我们现在可以在云端存储大量数据，而不用担心限制。  
&emsp;现在，让我们讨论各种驱动对于数据产生的贡献。  
&emsp;你有没有听说过IoT（物联网）?IoT将你的物理设备接入互联网，使它更智能。当今的社会，我们有智能空调，智能电视等等。你的智能空调能够根据外面的温度持久地控制你的室内温。想象一下，通过这些成千上万的空调，一年中可以产生多少数据。现在你应该可以明白IoT是如何贡献给大数据。  
&emsp;现在，我们一起来讨论大数据的最大贡献者：社交媒体。社交媒体是大数据演化过程中最重要的因素，它提供了关于用户行为习惯的信息。你可以通过下图查看它每分钟可以产生多少大的数据量:
![社交媒体数据生成](../images/hadoop/Social-Media-Hadoop-Tutorial-Edureka-768x743.png)
&emsp;除了生成数据的速度之外，大数据带来的第二个挑战是数据缺乏适当的格式或结构。   

## 3.0 大数据与Hadoop:餐厅类比
&emsp;关于Hadoop是如何解决大数据问题，我们用一个餐厅的例子来帮助我们理解。  
&emsp;Bob有一家小餐厅。起初，他每小时收到两份订单，而他的餐厅里有一个厨师和食品架，这些足以处理所有的订单。
![传统餐厅情景](../images/hadoop/Traditional-Restaurant-Analogy-Hadoop-Tutorial-Edureka.png)
&emsp;现在我们用餐厅来类比数据的产生是以一种稳定的速率，以及我们的关系型数据库(RDBMS)足以处理它，就像Bob餐厅里的厨师。现在，我们可以把数据存储想象成食品架，传统的数据处理系统想象成如图中的厨师。
![传统情景](../images/hadoop/Traditional-Scenario-Failed-Hadoop-Tutorial-Edureka.png)
&emsp;在几个月之后，Bob考虑扩大业务。因此他开始接一些网上订单，添加了一些新的食品来满足广大的顾客。因为这个转变，接收到的订单数量发生了巨大的增量，已增长到了每小时10份订单。以当前的状态，一个厨师难以完成如此巨大的订单数量。考虑到这种情况，Bob开始考虑解决方案。
![分布式厨师](../images/hadoop/Distributed-Chef-Hadoop-Tutorial-Edureka.png)
&emsp;相似地，在大数据理论中,当通过社交媒体、智能手机等媒介推荐后，数据会发生巨大的增长。现在，传统的应用，就像Bob的餐厅那样，已经没有足够的能力处理这种改变。然而，这就需要一些不同的解决方案来处理这些问题。  
&emsp;在进行了大量研究之后，Bob想出了解决，他雇佣了四个厨师来处理接收的大量订单。一切都很顺利，但是，这个解决方案面临一个新的问题。当四个厨师分享同一个食品架的时候，这个食品架就成为了这个流程中的瓶颈。因此，这个处理方案并没有Bob想得那个有效。  
![分布式处理方案失败](../images/hadoop/Distributed-System-Limitations-Hadoop-Tutorial-Edureka.png)
&emsp;相同地，为了解决大数据的问题，我们会建立很多处理单元来并行处理数据（就像Bob雇佣了4个厨师那样）。但是在这个案例中，多单元并行处理并没有有效地处理问题，因为集中式存储会成为瓶颈。换而言之，整个系统的性能将取决于集中式存储。当我们的集中式数据存储宕机，整个系统也将无法运行。因此，我们必须处理单点故障。
![餐厅问题解决方案](../images/hadoop/Restaurant-Solution-Hadoop-Tutorial-Edureka-768x362.png)
&emsp;Bob想出了另一张解决方案，他将所有的厨师分为两个等级：初级和高级厨师，他让初级厨师使用食品架。我们假设当天的餐点是肉酱。根据Bob的计划，一个初级厨师将准备肉，而另一个初级厨师将准备酱。然后他们将肉和酱提供给高级厨师，高级厨师将他们准备的肉和酱进行混合和加工，并交付最终的订单。
![hadoop餐厅方案](../images/hadoop/Hadoop-as-a-Solution-Restaurant-Analogy-Hadoop-Tutorial-Edureka.png)
&emsp;Hadoop的功能类似于Bob的餐厅。在Hadoop中，数据以一种分布式方式存储，并且为了容错性进行了分片，类似于Bob餐厅中的食品架。为了并行处理，首先数据将被子节点处理，这些子节点中存储了一些中间数据，然后由主节点对子节点的所有数据进行合并，并发送最后的结果。  
&emsp;现在，你应该明白了为什么大数据是个问题，并且Hadoop是如何处理这些问题的。我们来总结一下，大数据带来了三个主要的挑战。

* 第一个挑战是存储巨量数据。存储大量数据对于一个传统系统来说是不可能的。理由很明显，存储能力被单个系统节点锁限制，并且数据会以惊人的速度增长。
* 第二个挑战是存储复杂数据。我们知道存储是一个问题，但我告诉你这只是一个问题。数据不仅仅是巨大的，而且是多种格式的(非结构化、结构化、半结构化)。所以，你必须确保系统能够存储来自不同渠道的不同类型的数据。
* 最后我们一起来看第三个问题，数据处理速度。现在由于需要处理的数据量巨大，我们在处理大量数据上花费的时间也非常长。

&emsp;为了解决这些存储问题与处理问题，Hadoop创建了两个核心组件，HDFS和YARN。HDFS解决了存储问题，它以一种分布式的方式来存储这些数据，并且易于拓展。YARN解决了处理问题，它可以大量地减少在数据处理上的时间。我们一起来看看Hadoop吧。

## 4.0 什么是Hadoop
&emsp;Hadoop是一个开源软件框架，主要用于存储与处理大数据，它可以以一种分布式的方式被安装在大规模的集群中。Hadoop拥有Apache v2证书。Hadoop主要基于谷歌发表的关于MapReduce论文而开发，这篇论文为它提供了很多的函数式变成概念。Hadoop采用了java的编程语言，并且成为了Apache的顶级项目。Hadoop的主要开发者为Mike Cafarella和Doug Cutting。

## 5.0 Hadoop解决方案
&emsp;我们来理解Hadoop是如何为大数据的问题提供解决方案的。
![hadoop解决方案](../images/hadoop/Hadoop-as-a-Solution-What-is-Hadoop-Edureka.png)

### 5.1 巨量数据
&emsp;根据上图，HDFS提供了分布式的方式来存储数据。你的数据可以被存储在DataNodes中的block里面，你可以控制每个block的大小。假设你有512mb的数据，而你设定HDFS只能创建128MB的block。HDFS将把数据切分成4个block(根据该计算公式得出的4个block，512/128=4)，并且存储在不同的DataNodes中。为了保证容错性，当这些数据block存入DataNodes时，这些数据Block也会被备份成多个存储在不同DataNodes中。  
&emsp;Hadoop主要根据横向扩展模型，而不是垂直缩放。在横向扩展中，你可以在运行时根据需要在HDFS集群中添加新的节点，而不是在节点上增加硬件资源。  

### 5.2 复杂数据
&emsp;根据上图，HDFS可以存储所有种类的数据，无论是结构化，半结构化还是非结构化的。在HDFS中，没有对数据的预先验证。它也遵循了一次写入多次读写的模型。正因如此，你可以一次写入多个种类的数据，多次读取来进行数据解析。

### 5.3 快速处理
&emsp;为了解决这个问题，我们将处理单元移动到数据上，而不是将数据移动到处理单元。所以，什么叫将计算单元移动到数据上？它意味着，处理逻辑被发送到数据存放的节点，以便于每个节点都可以并行处理一部分的数据，而不是将数据从不同的节点移动到单一的节点来进行处理。最后，每个节点的输出结果被合并到一起，最终的响应结果将被返回给客户端。

## 6.0 Hadoop特性
![hadoop特性](../images/hadoop/Hadoop-Features-Hadoop-Tutorial-Edureka.png)

### 6.1 可靠性
&emsp;当机器并行工作时，如果有一台机器故障了，其他的机器将会接管这个工作。Hadoop就是基于这样容错机制创建的，因此，它具有很高的可靠性。

### 6.2 经济性
&emsp;Hadoop采用可以在通用硬件上搭建，例如:你的台式电脑或者笔记本。例如，在一个很小的hadoop集群，所有你的DataNodes可以配置很小的资源，例如:8-16G的内存，5-10TB的硬盘以及Xeon处理器。但是相比同样基于硬件RAID的ORACLE，我可能花费5倍以上的资源。所以，采用hadoop的花费是十分廉价的。在hadoop的维护方面，他的维护性也是十分便捷以及经济的。而且，Hadoop也是一个开源软件，没有如何证书方面的费用。

### 6.3 易扩展性
&emsp;Hadoop具有与基于云的服务无缝对接的内置功能。所以，如果你在云上安装Hadoop，你不要担心拓展性，因为你的尽管安装更多的硬件，并且可以在任何时候只需要几分钟即可拓展你的服务。

### 6.4 灵活性
&emsp;Hadoop是十分灵活的，它可以处理任何种类的数据。我们叫他"多样性"在我们之前的[大数据入门](https://www.edureka.co/blog/big-data-tutorial)博客中。数据可以是多样的，而Hadoop都可以保存以及处理它们，不管它是结构化的、非结构化的还是半结构化的。    
  
&emsp;这4个特性使Hadoop成为大数据解决方案的先驱者。现在我们知道了什么是Hadoop，我们可以探索Hadoop的核心组件。让我们一起了解，Hadoop有哪些核心组件?

## 7.0 Hadoop核心组件
&emsp;当搭建一个Hadoop集群的时候，你可以选择一些服务作为你Hadoop平台的一部分，但是有两个服务是强制的。一个是HDFS（存储），而另一个是YARN（数据处理）。HDFS是Hadoop Distributed File System，它是一个可拓展的存储单元，而YARN被用来以一种分布式串行方式处理HDFS中的数据。  

### 7.1 HDFS
&emsp;我们先来谈谈HDFS。HDFS的核心组件有两个:NameNode与DataNode。我们先来谈谈这两个组件的功能:
![hadoop-HDFS](../images/hadoop/HDFS-Hadoop-Tutorial-Edureka-768x326.png)

#### 7.1.1 NameNode

* 它是一个主守护程序，用于维护和管理DataNodes(从属节点)。
* 它记录了在集群中所有block的元数据，例如:block的所在位置，文件的大小，权限等等。
* 它记录了在文件系统中每个元数据以及每次的变化
* 如果文件在HDFS被删除，NameNode会立即将其记录在编辑日志中。
* 它会经常接收到心跳检查，它会接收在集群中所有的DataNodes中的block报告来确保DataNodes是否存活。
* 它保存了在HDFS和DataNodes中所有的block信息，包括他们存放在哪里。
* 它具有高可用性和联合功能，我将在[HDFS架构](https://www.edureka.co/blog/hdfs-tutorial)中详细讨论。

#### 7.1.2 DataNode

* 它是一个从属节点，运行在每个子节点上。
* 所有的实际数据都保存在DataNodes中。
* 它主要负责从客户端中提供读写请求。
* 它主要负责创建block，删除block以及在其他的NameNodes中备份。
* 它会周期性地发送心跳检查给NameNode来汇报HDFS集群的健康，一般默认频率为3秒。

&emsp;所以，这就是所有关于HDFS的简单描述。我们来谈谈第二个Hadoop的关键组件:YARN。

### 7.2 YARN
&emsp;YARN也由两个关键组件构成：ResourceManager 和 NodeManager。
![hadoop-YARN](../images/hadoop/YARN-Hadoop-Tutorial-Edureka.png)

#### 7.2.1 ResourceManager

* 它是一个集群级别(每个集群一个)，在主节点上运行。
* 它管理在YARN中运行应用的资源和调度。
* 它有两个组件:Scheduler 和 ApplicationManager。
* Scheduler负责分配资源。
* ApplicationManager负责接收job提交，协商执行应用的顺序。
* 它负责追踪每个节点心跳检查。

#### 7.2.2 NodeManager

* 它是一个节点级别（每个节点一个），运行在每个子节点上。
* 它负责管理container，监控container的资源利用。
* 它负责追踪每个节点的健康和日志管理。
* 它会持续地向ResouceManager联络，保持信息更新。

### 7.3 Hadoop生态
&emsp;现在你知道Hadoop不是一种编程语言或者服务，它是一个平台或者框架，用于处理大数据带来的问题。你可以想象它是一整套的服务，用于获取、存储以及分析大量数据。
![hadoop-生态](../images/hadoop/Hadoop-Ecosystem-Hadoop-Tutorial-Edureka.png)
&emsp;我们可以讨论Hadoop的具体细节在[Hadoop生态系统博客](https://www.edureka.co/blog/hadoop-ecosystem)中。现在，让我们一起讨论一个案例，来介绍Hadoop的使用。

## 7.3 Last.fm案例研究
&emsp;Last.fm是一家成立于2002年的互联网广播和社区驱动的音乐服务公司。用户在Last.fm传递信息，告诉大家他们正在听什么歌。然而，Last.fm可以针对你的喜好作智能推荐。这些数据主要来源于以下两个方面:

* **歌曲记录:**当一个用户正在播放他选择的曲目时，这些信息将会通过客户端传输到Last.fm中。
* **收音机:**当用户将收音机频道转向Last.fm，并收听音乐时。

&emsp;Last.fm应用可以允许用户喜欢、跳过或者禁止任何歌曲。这些歌曲数据也会被传输到Last.fm的服务器中。

- 每月超过40M独立访问者，500M网页浏览数
- 歌曲记录状态:
  + 每秒超过800的音乐记录
  + 每天超过4千万的音乐记录
  + 至今有超过750亿的音乐记录
- 收音机状态:
  + 每月超过1千万的收听数量
  + 每天超过40万个独立站点
- 每个歌曲记录和收音机都会产生至少一个记录线

&emsp;Last.fm的Hadoop集群规模:
- 100个节点
- 每节点8核CPU（双四核）
- 每节点24G内存
- 每节点8T硬盘(4个2T硬盘)
- 结合了HIVE来优化SQL查询，提升分析效率

&emsp;Last.FM在2006年开始使用Hadoop，因为他们的用户已经从几千人增长至几百万。在Hadoop的帮助下，他们处理了可以处理上百个每日、每月以及每周工作，包括网站统计和指标、图表(例如:跟踪统计)、元数据更正(例如：拼写错误)、搜索索引创建、组合/格式化推荐数据、数据洞察以及评估和报告。这帮助了Last.fm在短期内快速增长来发现客户需求。  
&emsp;我希望这篇博客可以对你有所帮助。在我们的下一篇博客[Hadoop生态系统博客](https://www.edureka.co/blog/hadoop-ecosystem)中，我们将详细地介绍Hadoop生态中不同的工具。

注：这篇文章翻译自:https://www.edureka.co/blog/hadoop-tutorial/ 

