---
title: "Spring Bean的生命周期"
date: 2019-12-19T15:20:59+08:00
draft: true
tags: ["spring"]
categories: "microservice"
toc: true
---
&emsp;本期我们将介绍下spring IoC容器中，Spring Bean的生命周期。我们先来看一张整体的架构图:

![spring-bean的生命周期](../images/spring/spring-bean的生命周期.png)

&emsp;我们将这张图上的阶段，分为四个部分：

1. 实例化
2. 属性赋值
3. 初始化
4. 销毁 


## 1. 实例化
&emsp; Spring IoC会将在XML、java注解或代码中定义的Bean definition实例化Bean对象。

## 2. 属性赋值
&emsp;读取Bean定义的所有属性，通过注入的方式对所有属性进行赋值。Spring有两种注入方式，分别为:构造器方法、setter方法(默认)。

&emsp;接着，就需要调用执行Aware接口。Aware接口的作用就是可以从Spring容器中获取资源。一般来说，按照顺序，有如下三个常用的Aware接口:

1. **BeanNameAware:** 执行`setBeanName`的方法，为bean定义id的值。
2. **BeanFactoryAware:** 指定`setBeanFactory`的方法,当选择BeanFactory作为容器的时候，工厂通过传递自身的实例来调用。
3. **ApplicationContextAware:** 指定`setApplicationContext`的方法,当选择ApplicationContext作为容器的时候，工厂通过传递自身的实例来调用。

## 3. 初始化
&emsp;在Bean完成了实例化以及基础的属性赋值后，会进行初始化，具体步骤如下:

1. 如果Bean定义了BeanPostProcessor，调用其中`preProcessBeforeInitialization` 方法。称为后置处理器，作用是在 bean 的实例化的过程中对 bean 进行自定义的包装处理。

2. 如果Bean实现InitializingBean接口，则会调用`afterPropertiesSet`方法。可以在其中定义一些初始化Bean的内容。

3. 如果Bean指定init方法（例如 <bean /> 的init-method属性），那么将调用该方法。他的作用与InitializingBean接口类似，可以一起使用。但init方法使用的反射调用的方式，比起afterPropertiesSet的直接调用，效率要低，优先级也低。不过，init可以消除对Spring的依赖。

4. 如果存在与Bean关联的任何 BeanPostProcessor们，则将调用`postProcessAfterInitialization`方法。

&emsp;截止这里，一个Spring Bean就创建好了，大家可以放心调用使用。

## 4. 销毁

&emsp;在Spring Bean使用完成后，可以进行销毁，销毁流程如下：

1. 如果实现了`disposableBean`接口与，那么自动执行`destroy`方法。等价于`InitializingBean`接口的`afterPropertiesSet`方法。
2. 如果Bean指定destory方法（例如 <bean />的destroy-method 属性），那么将调用该方法。等价于`init-method`属性。
