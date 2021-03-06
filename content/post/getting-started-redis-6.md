---
title: "redis入门实战之六:工作流(pipeline)"
date: 2019-04-22T20:22:42+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---
# 基本介绍
&emsp;在Redis中，客户端与服务器之间是通过RESP协议进行通信的。它们之间的通信过程可以看作：  
&emsp;1.客户端向服务器发送一个命令。  
&emsp;2.服务器接收该命令并将其放入执行队列。（因为Redis是单线程的）  
&emsp;3.命令被执行。  
&emsp;4.服务器将命令执行的结果返回客户端。  
&emsp;上诉过程所消耗的所有时间被称为往返时延（RTT，round-trip time）。在以上的步骤中，第二步和第三步主要取决于Redis服务器的处理时间，而第一步和第四步则需要取决于客户端和服务端之间的网络。如果要执行多个命令，可能在网络传输中就要耗费大量时间。  
&emsp;因此，Redis管道的基本思想在于:客户端将多个命令打包在一起，并一次性发送给服务器。客户端获得的不是每个单独的命令执行结果，而是等待所有命令执行后一次性获得所有的结果。相对于一个接一个命令的执行，管道所消耗的总时间会更少。  

# 操作实战
&emsp;先创建一个pipeline.txt文件，文件内容以下:
```jshelllanguage
~$ cat pipeline.txt 
set jovi model
set fran makeup
sad model gundam transformer
get jovi
```
&emsp;运行以下命令，执行pipeline
```jshelllanguage
~$ cat pipeline.txt | redis-cli --pipe
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 4
```
&emsp;查看结果
```jshelllanguage
~$ redis-cli 
127.0.0.1:6379> smembers model
1) "transformer"
2) "gundam"
```

# 工作原理
redis-cli中含有`--pipe`选项会一次性地发送所有来自stdin的命令，从而极大地减少往返时延的开销。

