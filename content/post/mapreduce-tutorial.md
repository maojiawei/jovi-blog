---
title: "Mapreduce入门"
date: 2019-01-08T13:26:04+08:00
draft: false
tags: ["hadoop"]
categories: "bigdata"
toc: false
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

## 4.0 MapReduce例子:字数统计
&emsp;我们来讲解个例子介绍MapReduce的运作。现在我有一个文件，名为:example.txt。这里面的内容为:
<strong><center><font size="5">Dear, Bear, River, Car, Car, River, Deer, Car and Bear</font></center></strong>
&emsp;现在，我们使用MapReduce来对example.txt进行字数统计。所以我们需要找到单独的单词，并对这些单词进行统计。
![MapReduce字数统计流程](../images/mapreduce/MapReduce-Anatomy-MapReduce-Tutorial-Edureka-1.png)

* 首先如图中所示，我们将输入的文件分割成三部分。这将在所有的map节点上完成。
* 其次，我们对每个map阶段中的单词进行标记，并对每个标记或单词进行硬编码。每个单词出现一次，就会给出对应的硬编码为1。
* 现在，一系列键值对(key-value型)数据已经产生，但只有单独的字母并且只有一个为1的值。所以，对于第一行中的单词(Dear Bear River)，我们有3个键值对-Dear,1;Bear,1;River,1。Map阶段仍在同样的节点上处理。
* 在map阶段结束后，当排序和清洗发生的时候，会进行分区处理，以便于所有有相同key的元祖将被发送到相应的reduce服务中。
* 所以，当排序和清洗之后，每个reduce服务将产生一个独特的key，一系列值将有一个对应的key。例如：Bear,[1,1];Car[1,1,1]等等。
* 现在每个reduce通过key来统计key对应的值。如图所示，reduce获取值的集合，例如:Bear的[1,1]。然后，他统计每个数字，并获得最终的结果，例如:Bear,2。
* 最终，所有的输出键值对将会被写入输出的文件中。

## 5.0 MapReduce优势
&emsp;MapReduce有两个最大的优势:  

1. 并行计算  
&emsp;在MapReduce中，我们将job分解，在多个节点上运行。每个节点同时运行一部分的job。所以，MapReduce主要基于分而治之的模式，它使得我们可以在不同的机器上处理数据。正因为在不同的机器上并行处理数据而不是单节点运行，处理速度的时间将会被大幅度的降低。详见下图：
![传统方式与MapReduce对比](../images/mapreduce/Data-Locality-MapReduce-Tutorial-Edureka-1.png)

2. 数据位置  
&emsp;在MapReduce中，我们将数据处理逻辑移动到数据节点上，而不是将数据迁移至数据处理单元上。在传统的系统中，我们经常讲数据传输至数据处理单元上处理。但是，随着数据的增长，数据变得非常巨大，这将会带来以下的问题：

* 移动巨大的数据是十分耗费资源的，并会恶化网络环境。
* 单节点处理将成为瓶颈。
* 主节点可能过度消耗而宕机。

&emsp;现在，MapReduce可以帮助我们克服这些困难。这是由于，我们可以从上图看出，数据是分布在不同的节点中的，每个节点处理在所属节点上的数据。这有几个优势：

* 将数据处理单元移动至数据节点的操作是非常高效的。
* 每个节点并行处理部分数据可以将处理时间降低。
* 每个节点处理部分数据，因而，可以避免节点过度负担。

## 6.0 MapReduce样例工程
&emsp;在详细讲解之前，我们来看一眼MapReduce的样例程序，这可以帮助我们了解基本的概念，知道如何实践运用MapReduce环境。我写了一个字数统计的样例代码。不用担心！如果你一看到代码，遇到问题不理解，只需要跟着我一起来看看这个MapReduce的每一部分。  
SourceCode:
```java
package co.edureka.mapreduce;
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.fs.Path;
 
public class WordCount{
    public static class Map extends Mapper<LongWritable,Text,Text,IntWritable> {
        public void map(LongWritable key, Text value,Context context) throws IOException,InterruptedException{
            String line = value.toString();
            StringTokenizer tokenizer = new StringTokenizer(line);
            while (tokenizer.hasMoreTokens()) {
                value.set(tokenizer.nextToken());
                context.write(value, new IntWritable(1));
            }
        }
    }

    public static class Reduce extends Reducer<Text,IntWritable,Text,IntWritable> {
        public void reduce(Text key, Iterable<IntWritable> values,Context context) throws IOException,InterruptedException {
            int sum=0;
            for(IntWritable x: values)
            {
                sum+=x.get();
            }
            context.write(key, new IntWritable(sum));
        }
    }

    public static void main(String[] args) throws Exception {

        Configuration conf= new Configuration();
        Job job = new Job(conf,"My Word Count Program");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(Map.class);
        job.setReducerClass(Reduce.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        job.setInputFormatClass(TextInputFormat.class);
        job.setOutputFormatClass(TextOutputFormat.class);
        Path outputPath = new Path(args[1]);
        // Configuring the input/output path from the filesystem into the job
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        // deleting the output path automatically from hdfs so that we don't have to delete it explicitly
        outputPath.getFileSystem(conf).delete(outputPath);
        // exiting the job only if the flag value becomes false
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
## 7.0 详解MapReduce程序
&emsp;我们现在将这个程序分为三部分：

* Map阶段代码
* Reduce阶段代码
* 驱动代码

&emsp;我们将依次讲解这三个部分的代码：  
&emsp;Map阶段代码：
```java
public static class Map extends Mapper<LongWritable,Text,Text,IntWritable> {
    public void map(LongWritable key, Text value,Context context) throws IOException,InterruptedException{
        String line = value.toString();
        StringTokenizer tokenizer = new StringTokenizer(line);
        while (tokenizer.hasMoreTokens()) {
            value.set(tokenizer.nextToken());
            context.write(value, new IntWritable(1));
        }
    }
}
```
![](../images/mapreduce/Input-Text-File-MapReduce-Tutorial-Edureka-1-258x300.png)
- 我们创建了一个Map类，继承了Mapper类，这个类已经在MapReduce框架中被定义。
- 我们定义了输入的数据类型以及输出键值对的数据类型。
- Mapper的输入与输出都是键值对。
- 输入：
  + 键是文本文件中每行的偏移量：LongWritable
  + 值是每个独立行(如上图所示)：Text
- 输出：
  + key是被标识的文字：Text
  + 在我们的案例中硬编码的值：IntWritable
  + 例子-Bear,1;Dear,1等等
- 我们可以写一段java代码，来标识每个单词，将他们硬编码的值变成1。

&emsp;Reduce阶段代码：
```java
public static class Reduce extends Reducer<Text,IntWritable,Text,IntWritable> {
    public void reduce(Text key, Iterable<IntWritable> values,Context context) throws IOException,InterruptedException {
        int sum=0;
        for(IntWritable x: values)
        {
            sum+=x.get();
        }
        context.write(key, new IntWritable(sum));
    }
}
```
- 我们可以创建一个Reduce类继承Reducer，类似Mapper。
- 在类创建之后，我们定义了输入和输出的键值对数据类型。
- Reduce的输入和输出都是键值对。
- 输入：
  + 在排序和清洗之后，key为那些唯一的单词:Text。
  + value对一系列的int类型对应每个key: IntWritable。
  + 例子-Bear,[1,1]等
- 输出：
  + key是所有输入的文字中独一无二的单词：Text。
  + value为这些单词出现的数量：IntWritable。
  + 例子-Bear,2;Car,3 等等。
- 我们合并了这个集合中所有的value结果，并产生最终的结果。
- 一般来说，每个单独的单词将会有一个Reduce负责处理，但是你可以在mapred-site.xml配置文件中定义reduce的数量。

&emsp;驱动程序：
```java
public static void main(String[] args) throws Exception {
    Configuration conf= new Configuration();
    Job job = new Job(conf,"My Word Count Program");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(Map.class);
    job.setReducerClass(Reduce.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    job.setInputFormatClass(TextInputFormat.class);
    job.setOutputFormatClass(TextOutputFormat.class);
    Path outputPath = new Path(args[1]);
    // Configuring the input/output path from the filesystem into the job
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    // deleting the output path automatically from hdfs so that we don't have to delete it explicitly
    outputPath.getFileSystem(conf).delete(outputPath);
    // exiting the job only if the flag value becomes false
    System.exit(job.waitForCompletion(true) ? 0 : 1);
}
```
- 在驱动程序中，我们可以定义MapReduce的job在Hadoop中运行。
- 我们可以定义job的名称，mapper和reducer对应的数据输入与输出。
- 我们可以定义mapper和reducer的名称。
- 输入与输出文件的路径也需要被定义。
- `setInputFormatClass`方法可以用来定义：一个Mapper如何读取数据。然而，我们可以选择TextInputFormat以便于mapper可以读取一次读取文本文件的单行。
- `main`方法是驱动的入口。在这个方法中，我们可以定义job。

&emsp;运行Code:
```shell
hadoop jar hadoop-mapreduce-example.jar WordCount /sample/input /sample/output
```
&emsp;现在你已经了解了MapReduce的基础。你可能意识到MapReduce帮助我们写代码从HDFS中处理大量数据。相比Hadoop1.x，Hadoop2.x的MapReduce有着巨大的改变。这些改变将会在下一篇博客：MapReduce入门系列中讨论。我将分享一个可下载的复杂教程，为大家分享MapReduce程序。

&emsp;注：这篇文章翻译自:https://www.edureka.co/blog/mapreduce-tutorial/
