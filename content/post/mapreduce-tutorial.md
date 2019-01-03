---
title: "Mapreduce入门"
date: 2019-01-03T13:26:04+08:00
draft: false
categories: "bigdata"
---
## 1.0 引言
&emsp;在这篇博客中，我将向你推荐MapReduce，这在Hadoop框架中是一个核心的数据处理模块。在开始了解之前,我建议你先熟悉HDFS的概念，而我已经介绍了这些相关内容在[HDFS入门](https://www.edureka.co/blog/apache-hadoop-hdfs-architecture/)中。这将帮助你更快更容易地了解MapReduce。  
&emsp;Google在2004年12月发表了一篇关于MapReduce论文。这成为了Hadoop数据处理框架的重要里程碑。所以，MapReduce是一个编程模型，他可以帮助我们并行地分布式地处理大数据。我们将从以下几个方面来介绍MapReduce:

* 传统方式的并行分布式数据处理
* 什么是MapReduce
* MapReduce例子
* MapReduce的优势
* MapReduce编程
* MapReduce编程详解

## 2.0 传统方式
![传统方式](../images/mapreduce/Traditional-Way-MapReduce-Tutorial-Edureka-4.png)
&emsp;我们一起来看看，如果没有MapReduce模型，并行分布式数据处理如何进行处理。所以，举个例子，我有一个天气日志，它收集了2000年至2015年的日平均气温。这里，我想要获得每一年气温最高的一天。  
&emsp;以传统的方式来，我们可以把数据分成很小的部门或者模块，并且存储在不同的机器中。然后，我们可以在相应的服务器上取最高的气温。最后，我们从所有的机器上合并结果，来找到最终的结果。那么这个传统的方式会带来如下挑战:  
1. **关键路径问题：** 关键路径是指所有任务中最长的路线，决定了整个工作的总实际耗时。所以，当有任何的机器延迟了任务，那么所有的工作将被延迟。  
2. **可靠性问题：** 如果任何的机器在处理部分数据的时候失败了，会怎么样呢？对于容错性的管理成为了挑战。  
3. **平等拆分问题：** 我们应该分割数据以便于所有的机器都可以获得部分数据来处理。换而言之，如何能够均分数据来保证没有机器是过载或利用不足。  
4. **单点拆分故障：** 如果任何一个节点失败了，我可能就无法获得计算结果。所以，我们应该有容错机制。  
5. **结果合并：** 我们需要有一个机制来合并所有机器的结果并产生最终结果。  
&emsp;当我们使用传统方式的时候，这些问题都需要我们单独地关注处理。
&emsp;为了克服这些问题，就有了MapReduce，它可以让我们不用担心这些问题(可靠性，容错性等等)就可以进行并行计算。然而，MapReduce给我们一个更灵活的方式来书写代码逻辑。

## 3.0 什么是MapReduce
![MapReduce模型](../images/mapreduce/MapReduce-Anatomy-MapReduce-Tutorial-Edureka-1.png)
&emsp;MapReduce是一个编程框架，它可以让我们在分布式环境中以并行方式和分布式来处理大数据。

* MapReduce有两个独立的任务-Map和Reduce。
* 根据MapReduce的建议，reduce阶段在Map阶段完成后发生。
* 所以，map的一个工作的开始，主要负责一系列数据的读取和处理，并产生key-value型数据作为中间数据的输出。
* map阶段的结果对作为reduce阶段的输出。
* reduce阶段从多个map阶段的结果中获取key-value数据。
* reduce阶段合并所有的中间数据，作为一个更小的数据集作为最终结果。
