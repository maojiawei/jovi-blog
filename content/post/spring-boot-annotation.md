---
title: "Spring Boot注解:@SpringBootApplication"
date: 2019-10-30T10:45:33+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: true
---
&emsp;在Spring boot中，有一个最重要的注解，在main函数上有一个重要的注解，即`@SpringBootApplication`。

&emsp;该注解包含了`@ComponentScan`、`@SpringBootConfiguration`和`@EnableAutoConfiguration`注解。

### 1.0 ComponentScan
&emsp;`@ComponentScan`会自动扫描指定包下(默认为启动类所在包下) 标有特定注解的类，将其注册为bean，并结合`@Autowired`注解导入。这些注解主要包括,但不仅限于:

- @Controller
- @Component
- @Service
- @Repository

### 2.0 SpringBootConfiguration
&emsp;SpringBootConfiguration是继承自`@Configuration`。标注当前类为配置类，并会将当前类声明的一个或多个以`bean`注解标记方式纳入spring容器中，实例名即为方法名。主要为了表明这个类是Spring Boot的主配置类，Spring Boot应该运行这个类来启动SpringBoot应用。

### 3.0 EnableAutoConfiguration
&emsp;`@EnableAutoConfiguration`会自动扫描指定包下(默认为启动类所在包下) 标有`@Configuration`的配置，加载至当前的IoC容器中，并结合`@Autowired`注解导入。
