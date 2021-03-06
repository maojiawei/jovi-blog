---
title: "redis入门实战之一:入门篇"
date: 2019-02-19T18:31:35+08:00
draft: false
tags: ["redis"]
categories: "microservice"
toc: true
---
## 1.0 简介
&emsp;Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。

## 2.0 数据类型

&emsp;Redis具有丰富的数据类型，常用的类型主要是String,List,Hash,Set,ZSet这五种。

<table>
    <thead> 
        <th width="50">数据类型</th>
        <th width="50">描述</th>
        <th >详细说明</th>
    </thead>   
    <tbody>
        <tr>
            <td>STRING</td>
            <td>字符串、整数或浮点数</td>
            <td>对整个字符串或者字符串中的一部分执行操作<br/>对整个整数或者浮点执行自增(increment)或者自减(decrement)操作。</td>
        </tr>
        <tr>
            <td>LIST</td>
            <td>链表</td>
            <td>一个链表，链表上的每个节点都包含了一个字符串，从链表的两端推入或者弹出元素，根据偏移量对链表进行修剪(trim)，读取单个或者多个元素，根据值查找或者移除元素。</td>
        </tr>
        <tr>
            <td>SET</td>
            <td>无序集合</td>
            <td>包含字符串的无序收集器(unordered collection)、并且被包含的每个字符串都是独一无二的。添加，获取，移除单个元素，检查一个元素是否存在于集合中，计算交集，并集，差集，从集合里面随机获取元素。</td>
        </tr>
        <tr>
            <td>HASH</td>
            <td>包含键值对的无序散列对</td>
            <td>包含键值对无序散列表，添加，获取，移除当键值对，获取所有键值对。</td>
        </tr>
        <tr>
            <td>ZSET</td>
            <td>有序集合</td>
            <td>字符串成员(member)与浮点数分值(score)之间的有序映射，元素的排列顺序由分值的大小决定。添加，获取，删除单个元素，根据分值范围(range)或者成员来获取元素。</td>
        </tr>
    </tbody>
</table>

## 3.0 用途

&emsp;正是因为这些丰富的数据结构，Redis可以应用于很多方面，包括数据库，缓存、消息队列等等。

### 3.1 缓存

&emsp;Redis主要使用内存作为存储，因此它会比类似于mysql、oracle这样的传统的基于硬盘的数据库会快很多。当有数据需要大量读取或者高并发时，Redis是一种很好的技术选型。

### 3.2 排行榜

&emsp;排行榜主要利用ZSET(有序集合)的特点，可以轻松实现一个大型列表中取得排名最高的N个元素，加上基于内存的特性，这个效率是非常快的，几乎能达到毫秒级。

### 3.3 SESSION存储

&emsp;相比于Memcache，Redis具有缓存数据持久化的能力。如果Redis由于故障重启后，Redis还能重新加载还原数据，避免了session突然消失带来的用户体验问题。

### 3.4 消息队列

&emsp;Redis的链表是双向链表，所在在列表头尾插取数据都是非常快的，因此可以基于这个特性实现一个简单的队列。

### 3.5 发布/订阅模式

&emsp;pub/sub是Redis内置的一个非常强大的特性，可以基于该特性创建一个实时的聊天系统、社交网络通知触发器等等。

