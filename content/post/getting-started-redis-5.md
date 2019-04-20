---
title: "redis入门实战之五:消息队列"
date: 2019-04-20T12:48:10+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---
# 基本介绍
&emsp;redis可以通过发布订阅的方式实现消息队列。在发布-订阅模式中，想要发布的事件(event)的发布者(publisher)会将通过消息(message)发送至对应的频道(channel)中，这个频道会事件投递(delviver)给想要看到消息的订阅者(subscriber)。   
&emsp;以下图为例，在下图中有一个频道:`channel1 `，并拥有三个客户端对该渠道进行订阅。  
![redis-消息订阅](../images/redis/消息订阅.svg)
&emsp;当有消息发送至对应频道`channel1`的时候,这个消息就对被发送给三个客户端。  
![redis-消息发送](../images/redis/消息发送.svg)

# 操作实战
&emsp;我们先创建一个消息订阅者(subscriber)。首先创建一个频道(channel)，在对这个频道进行订阅。
```shell
127.0.0.1:6379> subscribe jovi
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "jovi"
3) (integer) 1
```

&emsp;我们再启动一个新的客户端，然后向刚刚创建的频道发送消息，在订阅者的客户端即可收到消息。
```shell
127.0.0.1:6379> publish jovi aaa
(integer) 1
```

&emsp;订阅端收到结果如下
```shell
n1) "message"
2) "jovi"
3) "aaa
```

# 工作原理
&emsp;`SUBSCRIBE`可以用来监听特定频道中的可用消息，在使用的时候，如果之前没有被订阅过，会自动创建频道。一个可以端可以同时监听多个频道，也可以使用`PSUBSCRIBE`命令订阅匹配指定模式的频道。如果想要取消频道订阅，可以使用`UNSUBSCRIBE`来取消。  
&emsp;`PUBLISH`命令可以将一条消息发送至指定频道。订阅者可以通过频道收到消息。  
&emsp;注意:Redis的PubSub不支持消息化机制，如果Redis由于某种情况停机或重启，那么所有相关的对象都将丢失。  