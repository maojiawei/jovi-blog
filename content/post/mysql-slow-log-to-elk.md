---
title: "mysql慢日志查询（ELK方式）"
date: 2017-12-21T18:35:29+08:00
draft: false
tags: ["日志收集"]
categories: "microservice"
---

# 简介
本文介绍的是采用ELK（elasticsearch+logstash+kibana）处理mysql慢日志的方法。

## mysql搭建
本文采用的搭建mysql的方式是docker方式。
### 目录创建
首先创建目录  
mysql默认数据目录
```
mkdir /root/mysql
```
mysql日志存放目录
```
mkdir /var/log/mysql
touch /var/log/mysql/slow_query.log
```
### mysql服务
本文采用
```bash
docker run -d --name=mysql -e MYSQL_ROOT_PASSWORD=maojiawei7750 -p 3306:3306 -v /root/mysql:/var/lib/mysql -v /var/log/mysql:/var/log/mysql mysql:5.7
```
> ##### docker run -d 创建一个docker容器，并在后台运行容器  
> ##### --name=mysql 容器名称为mysql  
> ##### -e MYSQL_ROOT_PASSWORD=maojiawei7750  指定mysql的root密码  
> ##### -p 3306:3306 容器开放端口，mysql默认端口为3306  
> ##### -v /root/mysql:/var/lib/mysql mysql数据目录挂载  
> ##### -v /var/log/mysql:/var/log/mysql mysql日志目录挂载  
> ##### mysql:5.7 镜像名称，本例用的是mysql的官方镜像 5.7

### 开启慢日志
将日志文件设置为mysql的用户
```
docker exec -ti mysql chown mysql:mysql /var/log/mysql/slow_query.log
```
运行一下命令,输入root密码（本文为maojiawei7750），即可进入mysql中。
```
docker exec -ti mysql mysql -uroot -p
```
运行以下命令，查看慢日志输出是否开启，如果slow_query_log为OFF，则未开启（默认为不开启）。
```
mysql> show variables like '%slow%';
+---------------------------+--------------------------------------+
| Variable_name             | Value                                |
+---------------------------+--------------------------------------+
| log_slow_admin_statements | OFF                                  |
| log_slow_slave_statements | OFF                                  |
| slow_launch_time          | 2                                    |
| slow_query_log            | OFF                                  |
| slow_query_log_file       | /var/lib/mysql/bcb69cf0f7a2-slow.log |
+---------------------------+--------------------------------------+
5 rows in set (0.87 sec)
```
运行以下命令，指定日志输出文件。
```
mysql> set global slow_query_log_file='/var/log/mysql/slow_query.log';
Query OK, 0 rows affected, 1 warning (0.00 sec)  
```
运行以下命令，开启慢日志输出。
```
mysql> set global log_slow_queries=1; 
Query OK, 0 rows affected, 1 warning (0.00 sec)  
```
再次查看,成功
```
mysql> show variables like '%slow%';
+---------------------------+-------------------------------+
| Variable_name             | Value                         |
+---------------------------+-------------------------------+
| log_slow_admin_statements | OFF                           |
| log_slow_slave_statements | OFF                           |
| slow_launch_time          | 2                             |
| slow_query_log            | ON                            |
| slow_query_log_file       | /var/log/mysql/slow_query.log |
+---------------------------+-------------------------------+
5 rows in set (0.01 sec)
```
运行测试的sql语句
```
mysql> select sleep(30) as b, 10 as c;
+---+----+
| b | c  |
+---+----+
| 0 | 10 |
+---+----+
1 row in set (30.00 sec)
```
退出
```
mysql> exit
```
查看是否已写入慢日志
```
cat /var/log/mysql/slow_query.log
mysqld, Version: 5.7.20-log (MySQL Community Server (GPL)). started with:
Tcp port: 0  Unix socket: /var/run/mysqld/mysqld.sock
Time                 Id Command    Argument
# Time: 2017-12-22T10:36:54.538209Z
# User@Host: root[root] @ localhost []  Id:    13
# Query_time: 30.000247  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1513939014;
select sleep(30) as b, 10 as c;
```
## 系统调优
elasticsearch 6.0以上需要对系统进行调优
```
cat << EOF > /etc/security/limits.conf
* soft nofile 65536
* hard nofile 131072
* soft nproc 2048
* hard nproc 4096
EOF
cat << EOF > /etc/sysctl.conf
vm.max_map_count=655360
EOF
sysctl -p
```
## elasticsearch服务
### 服务创建
```
docker run -d --name=elasticsearch -p 9200:9200 docker.elastic.co/elasticsearch/elasticsearch:6.1.1
```
> ##### docker run -d 创建一个docker容器，并在后台运行容器  
> ##### --name=elasticsearch 容器名称为elasticsearch  
> ##### -p 9200:9200 容器开放端口，elasticsearch默认端口为9200  
> ##### docker.elastic.co/elasticsearch/elasticsearch:6.1.1 镜像名称，本例用的是elastic的官方镜像 6.1.1（具体可查看https://www.docker.elastic.co/）

## logstash服务
### 配置文件
logstash的配置文件
```
vim /root/logstash.conf

input {
  file {
    type => "mysql-slow"
    path => "/var/log/mysql/slow_query.log"
    codec => multiline {
      pattern => "^# User@Host:"
      negate => true
      what => "previous"
    }
  }
}
filter {
  grok {
    match => { "message" => "(?m)^#\s+User@Host:\s+%{USER:user}\[[^\]]+\]\s+@\s+(?:(?<clienthost>\S*) )?\[(?:%{IPV4:clientip})?\]\s+Id:\s+%{NUMBER:row_id:int}\n#\s+Query_time:\s+%{NUMBER:query_time:float}\s+Lock_time:\s+%{NUMBER:lock_time:float}\s+Rows_sent:\s+%{NUMBER:rows_sent:int}\s+Rows_examined:\s+%{NUMBER:rows_examined:int}\n\s*(?:use %{DATA:database};\s*\n)?SET\s+timestamp=%{NUMBER:timestamp};\n\s*(?<sql>(?<action>\w+)\b.*;)\s*(?:\n#\s+Time)?.*$" }
  }
  date {
      match => [ "timestamp", "UNIX", "YYYY-MM-dd HH:mm:ss"]
      remove_field => [ "timestamp" ]
  }
}


output{
  elasticsearch {
    hosts => [ "elasticsearch:9200" ]
    index => "mysqlslow"
  }
}
```
### 服务创建
```
docker run -d -p 4560:4560 -v /root/logstash.conf:/etc/logstash.conf -v /var/log:/var/log --link elasticsearch:elasticsearch --name=logstash docker.elastic.co/logstash/logstash:6.1.1 logstash -f /etc/logstash.conf
```
> ##### docker run -d 创建一个docker容器，并在后台运行容器  
> ##### --name=logstash 容器名称为logstash  
> ##### -p 4560:4560 容器开放端口，logstash默认端口为4560  
> ##### -v /root/logstash.conf:/etc/logstash.conf 容器配置文件挂载  
> ##### -v /var/log:/var/log 将需要监控的日志挂载到logstash镜像中  
> ##### --link elasticsearch:elasticsearch 关联elasticsearch  
> ##### logstash -f /etc/logstash.conf 启动目录  
> ##### docker.elastic.co/logstash/logstash:6.1.1 镜像名称，本例用的是elastic的官方镜像 6.1.1（具体可查看https://www.docker.elastic.co/）

## kibana服务
### 服务创建
```
docker run -d  --name=kibana  -p 5601:5601 --link elasticsearch:elasticsearch -e ELASTICSEARCH_URL=http://elasticsearch:9200 docker.elastic.co/kibana/kibana:6.1.1
```
> ##### docker run -d 创建一个docker容器，并在后台运行容器  
> ##### --name=kibana 容器名称为kibana   
> ##### -p 5601:5601 容器开放端口，kibana默认端口为5601  
> ##### --link elasticsearch:elasticsearch 关联elasticsearch  
> ##### -e ELASTICSEARCH_URL=http://elasticsearch:9200 设置ES的地址  
> ##### docker.elastic.co/kibana/kibana:6.1.1 镜像名称，本例用的是elastic的官方镜像 6.1.1（具体可查看https://www.docker.elastic.co/）

### 查看kibana
点击create index pattern，在kibana创建index。
![创建index patterns](../images/elk/kibana-create-index1.jpeg)
输入index匹配，输入在logstash的output中elasticsearch中的index。
![创建index patterns](../images/elk/kibana-create-index2.jpeg)
设置filter时间，选择不设置
![创建index patterns](../images/elk/kibana-create-index3.jpeg)
在discover中查看日志,可以查看所有的慢日志
![discover](../images/elk/discover.jpeg)

