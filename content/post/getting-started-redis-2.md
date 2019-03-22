---
title: "redis入门实战之二:安装与使用"
date: 2019-02-19T18:31:35+08:00
draft: false
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

&emsp;字符串类型包括字符串、浮点型和整型。常用的redis命令如下所示:

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

&emsp;hash 是一个string类型的field和value的映射表，hash特别适合用于存储对象。常用的redis命令如下所示:

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

### 2.5 列表类型(List)

&emsp;列表是简单的字符串列表，按照插入顺序排序。常用的redis命令如下所示:

1.命令:lpush ${key} ${value1} [${value2} ...]   
  描述:将一个或多个值插入列表头部，注意后面插入的数据会在前面  
  参数:${key}为redis的键 ${value1}为对应的数据，可以为多个  
  ```shell
  127.0.0.1:6379> lpush sports football
  (integer) 1
  127.0.0.1:6379> lpush sports basketball
  (integer) 2
  ``` 

2.命令:rpush ${key} ${value1} [${value2} ...]   
  描述:在列表中添加一个或多个值，注意后面插入的数据会在后面  
  参数:${key}为redis的键 ${value1}为对应的数据，可以为多个  
  ```shell
  127.0.0.1:6379> rpush sports tennis
  (integer) 3
  ``` 

3.命令:lrange ${key} ${start} ${end}   
  描述:获取列表指定范围内的元素列表  
  参数:${key}为redis的键 ${value1}为对应的数据，可以为多个  
  ```shell
  127.0.0.1:6379> lrange sports 0 10
  1) "basketball"
  2) "football"
  3) "tennis"
  ``` 

4.命令:llen ${key}   
  描述:获取列表长度  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> llen sports
  (integer) 3
  ``` 
  
5.命令:lpop ${key}   
  描述:移除列表的第一个元素  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> lpop sports
  "basketball"
  127.0.0.1:6379> lrange sports 0 10
  1) "football"
  2) "tennis"
  ``` 
  
6.命令:rpop ${key}   
  描述:移除列表的最后一个元素  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> rpop sports
  "tennis"
  127.0.0.1:6379> lrange sports 0 10
  1) "football"
  ```
  
### 2.6 集合类型(Set)

&emsp;Set是有String类型组成的集合。在集合中，所有成员都是唯一的。常用的redis命令如下所示:  
  
1.命令:SADD ${key} ${value1} [${value2} ...]  
  描述:向集合中添加一个或多个成员  
  参数:${key}为redis的键 ${value1}为需要添加的集合成员  
  ```shell
  127.0.0.1:6379> sadd language english
  (integer) 1
  127.0.0.1:6379> sadd language french
  (integer) 1
  # 当插入的值与集合中的数据重复时，则会显示0
  127.0.0.1:6379> sadd language english
  (integer) 0
  ```
  
2.命令:scard ${key}  
  描述:显示集合的总数  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> scard language
  (integer) 2
  ```

3.命令:smembers ${key}  
  描述:显示集合中的所有成员  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> smembers language
  1) "english"
  2) "french"
  ```

4.命令:spop ${key}  
  描述:移除集合中的一个随机成员，并返回移除成员  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> spop language
  "french"
  ```

5.命令:srandmember ${key} ${count}  
  描述:返回一至多个集合成员  
  参数:${key}为redis的键  ${count}需要返回的成员数量，默认为1  
  ```shell
  127.0.0.1:6379> srandmember language
  "english"
  ```

6.命令:srem ${key} [${value1} ...]  
  描述:移除一至多个集合成员  
  参数:${key}为redis的键  ${value1}为需要移除的集合成员，可以为多个  
  ```shell
  127.0.0.1:6379> srem language english
  (integer) 1
  ```

7.命令:sunion ${key1} [${key2} ...]   
  描述:返回指定集合中的并集  
  参数:${key1}为redis的键，可以为多个     

8.命令:sinter ${key1} [${key2} ...]   
  描述:返回指定集合中的交集  
  参数:${key1}为redis的键，可以为多个 

### 2.7 有序集合类型(Sorted Set)

&emsp;有序集合与集合一样，都不允许重复数据。但有序集合中的每一个成员都会关联一个double类型的分数，可以通过这些分数进行排序。有序集合的成员是唯一的，但是分数可以重复。  
&emsp;常用的redis命令如下所示:  

1.命令:zadd ${key} ${score1} ${member1} [${score2} ${member2} ...]   
  描述:向有序集合中添加一个或多个成员；如果成员已存在，则会更新成员分数  
  参数:${key}为redis的键 ${score}为成员对应的分数 ${member}为成员  
  ```shell
  127.0.0.1:6379> zadd database 2 mysql 1 oracle 3 access 2 sqlserver 4 redis
  (integer) 5
  ```

2.命令:zcard ${key}   
  描述:获得有序集合的成员总数  
  参数:${key}为redis的键  
  ```shell
  127.0.0.1:6379> zcard database
  (integer) 5
  ```

3.命令:zcount ${key} ${min} ${max}    
  描述:判断两个分值之间的共有多少个数据  
  参数:${key}为redis的键 ${min}为分数的最低值 ${max}为分数的最高值 
  ```shell
  127.0.0.1:6379> ZCOUNT database 3 6
  (integer) 2
  ```

4.命令:zrank ${key} ${member}     
  描述:返回有序集合中某个成员对应的索引  
  参数:${key}为redis的键 ${member}为需要查看索引的成员 
  ```shell
  127.0.0.1:6379> ZRANK database redis
  (integer) 4
  ```

5.命令:zrevrange ${key} ${start} ${stop} [${withscore}]     
  描述:返回有序集中指定区间内的成员，通过索引，分数从高到低  
  参数:${key}为redis的键 ${start}为最低的分数 ${stop}为最高的分数 ${withscore}表示是否需要显示分数，可不写 
  ```shell
  127.0.0.1:6379> zrevrange database 1 3
  1) "access"
  2) "sqlserver"
  3) "mysql"
  ```

6.命令:	zrevrangebyscore ${key} ${max} ${min} [${withscore}]     
  描述:返回有序集中指定区间内的成员，通过索引，分数从低到高  
  参数:${key}为redis的键 ${min}为最低的分数 ${max}为最高的分数 ${withscore}表示是否需要显示分数，可不写 
  ```shell
  127.0.0.1:6379> zrevrangebyscore database 3 1
  1) "access"
  2) "sqlserver"
  3) "mysql"
  4) "oracle"
  ``` 

7.命令:zrem ${key} ${member1} [${member2} ...]      
  描述:移除有序集合中的一个或多个成员  
  参数:${key}为redis的键 ${member}为需要查看索引的成员,可以为多个 
  ```shell
  127.0.0.1:6379> zrem database redis access
  (integer) 2
  ``` 

8.命令:zremrangebylex ${key} ${min} ${max}      
  描述:移除有序集合中给定的字典区间的所有成员  
  参数:${key}为redis的键 ${min}为最低的分值 ${max}为最高的分值 


