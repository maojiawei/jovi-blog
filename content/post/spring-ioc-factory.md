---
title: "Spring IOC 容器种类及实现机制"
date: 2019-12-19T10:18:49+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: true
---
&emsp;之前，我们简单介绍了一下[Spring IOC介绍](http://jovi.io/post/spring-ioc-introduce/)。

### 1.0 Spring IoC 容器种类

&emsp;Spring Ioc容器分为以下两种：

- **Spring BeanFactory:** 这一类别的容器只实现了最基本的容器功能。
- **ApplicationContext:** 这一类别的称为应用上下文，他是容器的高级形态，增加了许多面向框架的特性，同时对应用环境做了许多适配。

### 2.0 Spring IoC 容器架构

&emsp;下图为在Spring框架中，Spring Ioc的继承体系：
![Spring的Ioc容器](../images/spring/spring-ioc-design.jpeg)

- **BeanFactory:** 最基础的接口，最核心的方法主要是`getBean(String beanName)`用于根据spring bean的名称获取bean。
- **HierachicalBeanFactory：** 他除了BeanFactory的基础方法之外，增加了getParentBeanFactory的接口功能，使得子容器可以根据该接口访问父容器（若在 A 工厂启动并加载 Bean 之前， B 工厂先启动并加载了，那 B 就是 A 的父工厂）。
- **ConfigurableBeanFactory：** 该接口在 HierarchicalBeanFactory 的基础上增加了可配置的功能，包括注册别名、注册单例等，设置 Classloader 、是否缓存 Bean Metadata 、设置 TypeConverter 、 BeanPostProcessor 、配置 Bean 依赖等。

- **MessageSource：** 主要用来帮助开发人员处理复杂点的场景，例如：国际化、特定环境的配置等。
- **ResourceLoader：** 定义加载资源（classpath或者文件资源）的规则。
- **AutowireCapableBeanFactory：** 定义了容器中的Bean自动装配的规则。
- **ListableBeanFactory：** BeanFactory的延伸，能够枚举其所有bean实例的bean工厂实现。
- **ApplicationEventPublisher：** 这是个函数式接口，可以用来定义lambda表达式。

- **ApplicationContext：** 核心接口，继承了BeanFactory。
- **ConfigurableApplicationContext：** 定义了与配置和生命周期相关的方法。
- **WebApplicationContext：** 该接口定义了很多著名的应用属性名称，并绑定到程序启动的上下文中。
- **ThemeSource：** 主要用于如何定位相应的主题资源文件，包含:FixedThemeResolver,SessionThemeResolver和CookieThemeResolver。

### 3.0 初始化过程
&emsp;Spring IoC容器的初始化过程可以分为三步：

1.Resource定位,定位容器对应的配置文件，注解等。

2.将Resource定位好的资源载入到BeanDefinition。BeanDefinition是一种数据结构，主要用于描述Bean实例，包含属性、构造器方法等。

3.将BeanDefiniton注册到容器中。
