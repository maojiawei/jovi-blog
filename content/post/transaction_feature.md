---
title: "数据库事务的四大特性"
date: 2019-09-21T17:17:48+08:00
draft: false
tags: ["database"]
categories: "microservice"
toc: true
---
数据库事务一般包含四个特性,我们一般称为ACID。
### 1.0 原子性（Atomicity）
&emsp;事务中包含的所有操作，要么全部成功，要么全部失败。

### 2.0 一致性（Consistency）
&emsp;事务执行前和执行后，他们的状态必须处于一致。

&emsp;例如:A有100块钱，B有100块钱。A转给B 50元，那么最后，A有150元，B有50元。他们的总和还是200。

### 3.0 隔离性（Isolation）
&emsp;多个用户操作数据库时，每个用户开启一个事务，事务与事务之间不会互相干扰。

### 4.0 持久性（Durability）
&emsp;事务一旦被提交了，那么数据库的数据改变是永久性的。



