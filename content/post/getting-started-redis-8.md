---
title: "redis入门实战之八:Redis5.0 stream数据结构"
date: 2019-05-04T10:43:38+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---
&emsp;Redis5.0最大的新特性就是加入了新的数据结构:stream。他是一个新的强大的支持多播的可持久化的消息队列。其与现存的数据结构的差别在于:

<table>
    <thead> 
        <th width="50">List,Pub/Sub,Zset</th>
        <th width="50">Redis Stream</th>
    </thead>   
    <tbody>
        <tr>
            <td>List不能高效地从中间获取数据，时间复杂度:O(N)</td>
            <td>支持亿万级别数量的中间获取，时间复杂度:O(logN)</td>
        </tr>
        <tr>
            <td>List没有offset(抵消)的概念，如果成员删除后，无法确定最新的成员。</td>
            <td>每个msg都有一个唯一的id，老的成员淘汰，id不变</td>
        </tr>
        <tr>
            <td>Pub/Sub不支持持久化</td>
            <td>可以持久化，AOP、RDB都可以</td>
        </tr>
        <tr>
            <td>Pub/Sub不能consumer group的概念</td>
            <td>有，允许多个consumer消费从同一个stream中消费数据</td>
        </tr>
        <tr>
            <td>Pub/Sub的性能和订阅某个频道的client数量正相关</td>
            <td>不存在，stream是通过consumer进行消费的</td>
        </tr>
         <tr>
            <td>Zset不允许重复数据，不支持成员淘汰和block操作，内存开销大</td>
            <td>允许，支持时间线淘汰数据，支持block操作，基于radix tree和listpack，内存开销低</td>
        </tr>
         <tr>
            <td>ZSet支持删除任意元素</td>
            <td>不支持中间删除元素(log属性)，more compact and memory efficient</td>
        </tr>
    </tbody>
</table>

## 1.0 设计思路
&emsp;redis stream是主要是参考kafka的，具体见下图。stream的数据是按照seq_id有序排列的。 redis会记录stream里每个消费组最后消费的last_id以及还没有返回ack确认的id数据。每个消费组都有一个last_id, 即每个消费组都可以消费同一条数据，每个消费组里可以有多个消费者，多个消费者是通过竞争获取消息。   
&emsp;比如同一条数据，会有两个以上的系统进行消费，那可以定义多个消费组来实现。 如果在同一系统中，吞吐量不够的情况下，可以定义多个消费者进行消费。 redis stream是可以保证这些操作原子性的。  
&emsp;stream又维护了一个pending_ids的数据，他的作用是维护那些未确认的id。比如某个消费者获取了消息，但后面突然异常宕机了。redis stream维护了这些没有xack的id，可以允许通过xpending来遍历这些数据，xpending是有时间信息的，我们可以在代码层过滤一个小时还没xack的id。  
![Redis虚拟槽](../images/redis/redis-stream.png)

## 2.0 具体操作

### 2.1 XADD

&emsp;XADD 可以向stream中插入数据，mystream为stream的key，*代表由redis为这条消息生成序列（一般为时间序列-序号，同一个ms内创建多个msg的情况，后面的id自增），stream可以有多个键值对。
```editorconfig
127.0.0.1:6379> xadd mystream * name jovi age  25
"1557662399756-0"
127.0.0.1:6379> xadd mystream * name even age  30
"1557662493436-0"
127.0.0.1:6379> xadd mystream * name fran age  18
"1557662508673-0"
```
&emsp;以下是同一个ms中创建多个msg的情况
```editorconfig
127.0.0.1:6379> multi
OK
127.0.0.1:6379> xadd mystream1 * name jovi
QUEUED
127.0.0.1:6379> xadd mystream1 * age 20
QUEUED
127.0.0.1:6379> exec
1) "1557662691852-0"
2) "1557662691852-1"
```
### 2.2 XRANGE

&emsp;查询从大到小的数据，-是最小，+是最大。

```
127.0.0.1:6379> xrange mystream - +
1) 1) "1557662399756-0"
   2) 1) "name"
      2) "jovi"
      3) "age"
      4) "25"
2) 1) "1557662493436-0"
   2) 1) "name"
      2) "even"
      3) "age"
      4) "30"
3) 1) "1557662508673-0"
   2) 1) "name"
      2) "fran"
      3) "age"
      4) "18"
```

&emsp;xrange可以限定结果集的大小。
```editorconfig
127.0.0.1:6379> XRANGE mystream - + count 2
1) 1) "1557662399756-0"
   2) 1) "name"
      2) "jovi"
      3) "age"
      4) "25"
2) 1) "1557662493436-0"
   2) 1) "name"
      2) "even"
      3) "age"
      4) "30"
```

&emsp;xrange可以指定时间戳之后的n条msg,可以理解为去某个时间段区间的内容
```editorconfig
127.0.0.1:6379> XRANGE mystream 1557662493436 + count 10
1) 1) "1557662493436-0"
   2) 1) "name"
      2) "even"
      3) "age"
      4) "30"
2) 1) "1557662508673-0"
   2) 1) "name"
      2) "fran"
      3) "age"
      4) "18"
```

### 2.3 XREAD
&emsp;XRANGE类似于LRANGE，可以一次性获取一批消息。而XREAD，类似于BLPOP，可堵塞，流式获取。我们开两个窗口，一个窗口通过XREAD监听，另一个窗口往stream写入数据。
&emsp;窗口1，输入以下命令，从两个stream同时获取数据，堵塞5000ms。$表示获取最新的，即下一条的msg。
```editorconfig
127.0.0.1:6379> XREAD BLOCK 5000 STREAMS mystream otherstream $ $

```
如果想指定某个时间之后，可以采用命令:
```editorconfig
127.0.0.1:6379> XREAD BLOCK 5000 STREAMS mystream otherstream $ 1557667084525-0
```
&emsp;窗口2，采用xadd 写入数据：
```editorconfig
xadd otherstream * message "hello strem"
"1557667084525-0"
```
&emsp;这是在窗口1显示以下结果：
```editorconfig
127.0.0.1:6379> XREAD BLOCK 5000 STREAMS mystream otherstream $ $
1) 1) "otherstream"
   2) 1) 1) "1557667084525-0"
         2) 1) "message"
            2) "hello strem"
(1.61s)
```

### 2.4 CONSUMER GROUP
&emsp;redis stream可以创建group，每个group有唯一的名字，每个名字内的consumer有唯一的名字。  
&emsp;创建group，创建两个group，一个为groupa,另一个为groupb
```editorconfig
127.0.0.1:6379> XGROUP CREATE mystream groupa $
OK
127.0.0.1:6379> XGROUP CREATE mystream groupb $
OK
```
&emsp;窗口1，塞入消息：
```editorconfig
127.0.0.1:6379> xadd mystream * message "hello consumer2"
"1557669262405-0"
127.0.0.1:6379> xadd mystream * message "hello consumer3"
"1557669264468-0"
127.0.0.1:6379> xadd mystream * message "hello consumer1"
"1557669147367-0"
```
&emsp;窗口2，获取消息
```editorconfig
# groupa 的返回消息
127.0.0.1:6379> xreadgroup group groupa consumer1 count 1 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1557669147367-0"
         2) 1) "message"
            2) "hello consumer1"
127.0.0.1:6379> xreadgroup group groupa consumer1 count 2 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1557669262405-0"
         2) 1) "message"
            2) "hello consumer2"
      2) 1) "1557669264468-0"
         2) 1) "message"
            2) "hello consumer3"
#group b的返回消息
127.0.0.1:6379> xreadgroup group groupb consumer1 count 2 streams mystream >
1) 1) "mystream"
   2) 1) 1) "1557669147367-0"
         2) 1) "message"
            2) "hello consumer1"
      2) 1) "1557669262405-0"
         2) 1) "message"
            2) "hello consumer2"
```



## 参考资料
http://xiaorui.cc/2018/06/07/%E6%B5%85%E5%85%A5%E6%B5%85%E5%87%BAredis5-0%E7%9A%84streams%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/
https://yq.aliyun.com/articles/495531
https://redis.io/topics/streams-intro