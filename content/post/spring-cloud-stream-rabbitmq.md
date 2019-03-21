---
title: "Spring Cloud Stream介绍（以Rabbitmq为例）"
date: 2017-12-24T16:51:41+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: false
---
# 简单介绍
之前我们介绍了如何使用spring boot整合rabbitmq来做微服务的消息中间件（[springboot与rabbitmq整合](http://jovi.io/post/springboot-rabbitmq-introduce/)），这篇文章将会使用spring cloud stream来整合rabbitmq实现消息队列。  
Spring cloud stream是为构建微服务消息驱动而产生的一种框架。Spring Cloud Stream基于Spring boot的基础上，可创建独立的、生产级别的Spring应用，并采用Spring Integration来连接消息中间件提供消息事件驱动。Spring Cloud Stream为不同的消息中间件提供了个性化的自动化配置实现，引用了发布-订阅、消费组、分区的三个核心概念。  
目前支持两种消息队列：  

> rabbitmq  
> kafka

# 应用模型

Spring Cloud Stream应用程序由一个中间件中立核心组成。应用程序通过Spring Cloud Stream注入的input和output通道与外界进行通信。通道通过中间件特定的绑定器实现连接到外部代理。
![Spring-cloud-stream-应用模型](../images/rabbitmq/spring-cloud-stream-with-binder.png)

# 前提准备
本例的rabbitmq的版本为rabbitmq:3.6.6-management，采用docker安装的方式。  
在安装docker的电脑上输入以下命令,浏览器中输入`localhost:15672`或者linux中运行`curl localhost:15672`即可  
```
docker run -d --name=rabbitmq -p 5672:5672 -p 15672:15672 -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin rabbitmq:3.6.6-management
```
```
docker run 表示docker启动容器。
--name表示容器名称。
-p 表示端口，rabbitmq的5672为AMQP协议端口，15672为管理界面端口。
-e 为对应参数，本例中默认的用户名密码为admin/admin
rabbitmq:3.6.6-management 表示镜像名称，本例默认为docker官方镜像
```

# 依赖添加
本文主要使用maven，在`pom.xml`加入对spring cloud stream与rabbitmq的支持。
```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.4.2.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
    </dependency>
</dependencies>
<!-- 引入spring cloud的依赖 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Camden.SR4</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```
编写项目启动类
```
@SpringBootApplication
public class SpringCloudStreamApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringCloudStreamApplication.class, args);
    }
}
```
加入配置文件,填写项目的启动端口与rabbitmq的相关信息
```
server:
  port: 8080
spring:
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: admin
    password: admin
```

# 消息通道
本例创建了SpringStreamChannel来实现消息通道（channel）,在这里定义了一对`nanhuiMqSpringStreamConsumer`和`nanhuiMqSpringStreamProducer`输入输出通道。
```
public interface SpringStreamChannel {
	/**
	 * 消息消费者
	 */
	String CONSUMER = "nanhuiMqSpringStreamConsumer";
	@Input(CONSUMER)
    SubscribableChannel consumer();
	/**
	 * 消息生产者
	 */
	String PRODUCER = "nanhuiMqSpringStreamProducer";
	@Output(PRODUCER)
    MessageChannel producer();
}
```

# 消息提供者
`@EnableBinding`该注解用来指定一个或多个定义了 @Input或 @Output注解的接口，以此实现对消息通道（Channel）的绑定。本例绑定了之前定义的Channel来发送消息。
```
@Slf4j
@EnableBinding(SpringStreamChannel.class)
public class SpringStreamMessageProducer {
    @Resource
    private SpringStreamChannel channel;
    public void produce(String message) {
        log.info("发送消息：{}", message);
        channel.producer().send(MessageBuilder.withPayload(message).build());
    }
}
```

# 消息消费者
同消息提供者，绑定Channel。
```
@Slf4j
@EnableBinding(SpringStreamChannel.class)
public class SpringStreamMessageConsumer {
    @StreamListener(SpringStreamChannel.CONSUMER)
    public void consume(String message){
        log.info("接收消息：{}", message);
    }
}
```

# 定义配置
下面的配置中`application/json`表示发送消息的格式(json)。同时我们绑定了之前定义的消息提供者与消息接收者。provider中destination为消息的目的地（类似于kafka的topic与rabbitmq的exchange），需要与消息接收者相同，exchangeType表示采用的是topic模式。consumer中destination与前消息提供者的destination相同，group为一个组（Spring Cloud Stream中每个group都会接收都消息，且只接收一次）。
```
spring:
  cloud:
    stream:
      default:
        contentType: application/json
      bindings:
        nanhuiMqSpringStreamProducer:
          destination: NANHUI_MQ_SPRING_STREAM_BEGIN
          exchangeType: topic
        nanhuiMqSpringStreamConsumer:
          destination: NANHUI_MQ_SPRING_STREAM_BEGIN
          group: PAAS_MYSQL_PROVIDER
```

# 案例
这里有我一个spring cloud stream的项目[案例](https://github.com/maojiawei/nanhui-mq)里面的nanhui-springcloud-stream有对应的代码资料，欢迎大家查看并给予意见。

# 相关文献
spring cloud stream官方文档 https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/  
Spring Cloud构建微服务架构：消息驱动的微服务（入门） https://mp.weixin.qq.com/s/bGJOStb-noU-oc2JpSulMw  
Spring Cloud Stream（消息驱动）http://blog.csdn.net/jack281706/article/details/73743148