---
title: "redis入门实战之二:安装与使用"
date: 2019-02-19T18:31:35+08:00
draft: true
tags: ["redis"]
categories: "microservice"
toc: true
---

本篇文章主要介绍redis的安装以及相关的使用。

## 1.0 安装

### 1.1 docker安装(推荐)

&emsp;本文推荐使用docker容器的方式进行redis的安装，首先有一台安装docker的mac或者linux服务器上。  
(1) **拉取镜像**  
   在命令行中输入以下命令，可以从[docker仓库](http://hub.docker.com)中拉取redis镜像。目前最新的redis官方的最新版本为5.0.3，我们在仓库中查找最新的稳定版本。
   ```shell
   docker pull redis:5.0.3-alpine
   ```
   命令说明:  
   **docker pull**为docker拉取镜像的命令。  
   **redis:5.0.3-alpine**为镜像名称及版本号,一般为**镜像:版本号**。  
   运行结果如下:  
   ![拉取镜像](../images/redis/docker-pull-redis.jpg)

(2) **启动容器**  
    在命令行中输入以下命令，可以启动一个redis的docker容器。  
   ```shell
   docker run -d --name=redis -p 6379:6379 redis:5.0.3-alpine
   ```
   命令说明:  
   **docker run**为docker启动容器。  
   **-d**为后台启动程序。  
   **--name=redis**为docker容器名称。  
   **-p 6379:6379**为开放端口，一般命令为-p ${host_port}:${container_port}。其中${host_port}为宿主机端口,${container_port}为容器内部端口。  
   **redis:5.0.3-alpine**为镜像名称及版本号,一般为**镜像:版本号**。  
   运行结果如下:  
   ![启动容器](../images/redis/docker-run-redis.png)

(3) **检查容器**  
    在命令行中输入以下命令，查看容器是否正常运行。  
   ```shell
   docker ps -a
   ```
   命令说明:  
   docker ps -a 查看容器。  
   运行结果如下,如果存在redis且状态为Up，说明创建成功:  
   ![检查容器](../images/redis/docker-ps-a-redis.png)

(4) **进入容器**  
    在命令行中输入以下命令，可以进入容器。  
   ```shell
   docker exec -ti redis sh
   ```
   命令说明:  
   **docker exec -ti**表示通过交互式方式进入容器。  
   **redis**是之前创建的容器名。  
   **sh**为进入的命令。  
   运行结果如下:  
   ![检查容器](../images/redis/docker-exec-redis.png)

## 2.0 操作
### 2.1 客户端
&emsp;如果为容器启动，需要首先进入容器中(主机安装可不用)。然后可以通过以下命令，进入redis的客户端。
```shell
redis-cli -h ${host} -p ${port} -a ${password}
```
命令说明:  
**redis-cli**为进入redis客户端命令。  
**-h ${host}**中的${host}为需要访问redis服务端的ip或主机名，默认当前主机可省略。  
**-p ${port}**中的${port}为需要访问redis服务端的端口，默认端口可省略。  
**-a ${password}**中的​${password}为需要访问redis服务端的密码，无密码可省略。  
运行结果如下:  
![redis客户端](../images/redis/redis-cli.png)

### 2.2 常用基本操作

&emsp;常用的redis命令如下所示:

1.命令:exist ${key}   
描述:检查给定key是否存在，返回1表示存在，0不存在  
参数:${key}为redis的键  
举例:

```shell
127.0.0.1:6379> exists demo
(integer) 1
```

2.命令:del ${key}  
描述:在key存在时用于删除key  
参数:${key}为redis的键  
举例:

```shell
127.0.0.1:6379> del demo
(integer) 1
```

3.命令:keys ${pattern}  
描述:根据匹配模式查找key  
参数:${pattern}为给定的匹配模式  
例如:demo:* 将会匹配所有key为demo:开头的key  
举例:

```shell
127.0.0.1:6379> keys demo:*
1) "demo:1"
2) "demo:3"
3) "demo:2"
```

4.命令:expire ${key} ${seconds}  
描述:为key设置生存时间   
参数:${key}为redis的键<br/> ${seconds}为保存的秒，单位:秒  
```shell
127.0.0.1:6379> expire demo:1 5
(integer) 1
``` 

5.命令:persist ${key}  
描述:移除key的过期时间，使key不管多久都会保存  
参数:${key}为redis的键  
```shell
127.0.0.1:6379> persist demo:1
(integer) 1
``` 

### 2.3 字符串类型(String)

&emsp;字符串类型包括字符串、浮点型和整型，常用的redis命令如下所示:

1.命令:set ${key} ${value}     
  描述:为指定的key设置值    
  参数:${key}为redis的键 ${value}为键对应的值    
  ```shell
  127.0.0.1:6379> set demo testvalue
  OK
  ``` 
2.命令:get ${key}  
  描述:获取指定key的值   
  参数:${key}为redis的键   
  ```shell
  127.0.0.1:6379> get demo
  "testvalue"
  ``` 
  
3.命令:setnx ${key} ${value}  
  描述:只有当key不存在时设置key的值    
  参数:${key}为redis的键 ${value}为键对应的值  
  ```shell
  # setnx一个已经存在的key会显示0，即没有任何key被改变
  127.0.0.1:6379> setnx demo 111
  (integer) 0
  127.0.0.1:6379> get demo
  "testvalue"
  # 如果不存在，才设置
  127.0.0.1:6379> setnx demonx demo1
  (integer) 1
  ``` 
  
4.命令:setex ${key} ${seconds} ${value}  
  描述:将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)   
  参数:${key}为redis的键 ${value}为键对应的值 ${seconds}为过期时间  
  ```shell
  127.0.0.1:6379> setex demoex 100 demo2
  OK
  ``` 

5.命令:setex ${key} ${seconds} ${value}  
  描述:将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)   
  参数:${key}为redis的键 ${value}为键对应的值 ${seconds}为过期时间  
  ```shell
  127.0.0.1:6379> setex demoex 100 demo2
  OK
  ``` 

5.命令:incr ${key}  
  描述:针对整数型数据，数字值加一  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> set democr 1
  OK
  127.0.0.1:6379> incr democr
  (integer) 2
  127.0.0.1:6379> get democr
  "2"
  ``` 
  
6.命令:decr ${key}  
  描述:针对整数型数据，数字值减一  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> decr democr
  (integer) 1
  127.0.0.1:6379> get democr
  "1"
  ``` 

### 2.4 哈希类型(Hash)

&emsp;hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象，常用的redis命令如下所示:

1.命令:hmset ${key} ${field1} ${value1} [${field2} ${value2} ....]  
  描述:同时将多个field-value(域-值)对设置到哈希表key中  
  参数:${key}为redis的键   
  ${field1}为键中对应的域(相当于对象的属性) ${value1}为域对应的值(可以为多个)  
  ```shell
  # person为key name与redis为域和对应的值 我们提供了name,age,interest给person这个对象
  127.0.0.1:6379> HMSET person name "redis" age "18" interest "ball"
  OK
  ``` 
  
2.命令:hgetall ${key}  
  描述:获取指定key的所有字段和值  
  参数:${key}为redis的键 
  ```shell
  127.0.0.1:6379> hgetall person
  1) "name"
  2) "redis"
  3) "age"
  4) "18"
  5) "interest"
  6) "ball"
  ``` 
3.命令:hget ${key} ${field}   
  描述:获取哈希表中指定字段对应的值  
  参数:${key}为redis的键 ${field}为键中对应的域
  ```shell
  127.0.0.1:6379> hget person name
  "redis"
  ``` 

4.命令:hexists ${key} ${field}   
  描述:查找哈希表中是否存在某个字段  
  参数:${key}为redis的键 ${field}为键中对应的域
  ```shell
  127.0.0.1:6379> hexists person name
  (integer) 1
  ``` 


5.命令:hincrby ${key} ${field} ${increment}  
  描述:查找哈希表中的数值型，进行自增  
  参数:${key}为redis的键 ${field}为键中对应的域 ${increment}增加的数量
  ```shell
  127.0.0.1:6379> hincrby person age 2
  (integer) 20
  127.0.0.1:6379> hget person age
  "20"
  ``` 
  
6.命令:hdel ${key} ${field1} [${filed2} ...]  
  描述:删除一个或多个哈希表字段  
  参数:${key}为redis的键 ${field1}为键中对应的域  
  ```shell
    127.0.0.1:6379> HDEL person interest
    (integer) 1
    127.0.0.1:6379> HGETALL person
    1) "name"
    2) "redis"
    3) "age"
    4) "21"
  ``` 

### 2.5 哈希类型(Hash)

&emsp;hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象，常用的redis命令如下所示:
  
  
