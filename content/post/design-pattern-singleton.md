---
title: "设计模式：单例设计模式"
date: 2019-11-22T13:41:54+08:00
draft: false
tags: ["spring"]
categories: "microservice"
toc: true
---
&emsp;单例是一种常用的软件设计模式，其定义就是在一个应用中，有且仅有一个实例存在。

### 1.0 原理
&emsp;一般来说，单例的实现通过两个步骤：

1. 在一个类中提供一个静态方法，当调用该方法时，就可以接收到单例实例。
1. 将对应类的构造方法改为私有方法，这样其他的类就无法通过构造器来实例化，只有通过静态方法才能获得实例。

### 2.0 实现方式
&emsp;单例的实现有两种方式，第一种称为懒汉式；第二种称为饿汉式。

#### 2.1 饿汉式
&emsp;饿汉式在应用启动的时候既会创建出一个单例对象，只需要通过调用`getInstance`即可返回单例对象。这种方式的优势在于线程安全，简单；劣势在于还没有使用的时候，初始化就完成了，容易造成资源浪费。

```java
public class Singleton{ 
     // 单例实例
     private static Singleton instance = new Singleton; 
     // 私有化构造方法
     private Singleton(){}
     // 静态方法 获取单例实例
     public static Singleton getInstance(){  
          return instance;  
      } 
}
```

#### 2.2 懒汉式
&emsp;懒汉式指的是只有在使用时才创建类。在静态方法中，我们先回判断类是否已经实例化，如果没有实例化就创建。但是在实际多线程访问的环境下，可能会有多个实例创建，因此我们通过加上了`synchronized`关键字来保证线程安全。
不过，这也导致了系统的性能下降。

```java
public class Singleton{ 
     // 单例实例
     private static Singleton instance = null; 
     // 私有化构造方法
     private Singleton(){}
     // 静态方法 获取单例实例
     public static synchronized  Singleton getInstance(){  
         if (instance == null) {
            instance = new Singleton();
         }
         return instance;  
      } 
}
```

#### 2.3 双重检测单例
&emsp;双重检测单例(Double-Check Locking)是懒汉式的一种特例。他继承了懒汉式延迟加载的特点，并在其基础上将`synchronized`关键字加在代码中，减少同步锁以提升性能。我们在instance实例上加上了violate字段，主要是避免在多线程环境下由于编译器进行的重排序操作而导致的线程安全问题。

```java
public class Singleton {
    // 单例实例
    private static volatile Singleton instance;
    // 私有化构造方法
    private Singleton() {
    }
    // 静态方法 获取单例实例
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

&emsp;为什么要做双重检测呢？因为如果以下这种写法，如果出现两个线程，线程A先实例化之后，线程B不用任何判断，依然会创建一个新实例。

```java
// 静态方法 获取单例实例
public static Singleton getInstance() {
    if (instance == null) {
        synchronized (Singleton.class) {
            instance = new Singleton();
        }
    }
    return instance;
}
```

#### 2.4 静态内部类单例
&emsp;静态内部类单例也是懒汉式中一种比较优秀的实现方式，这种方法的线程优点在于：

1. 静态内部类属于被动引用的类型，外部类加载时并不会立即加载内部类，内部类不会去加载instance实例，因此不占内存。

2. 在虚拟机中，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的初始化，其他线程都需要等待，直至方法执行完毕。因此保证了线程的安全性。

```java
public class   Singleton {
    // 私有化构造方法
    private Singleton() {

    }
    // 静态方法 获取单例实例
    public static Singleton getInstance() {
        return SingletonFactory.instance;
    }
    // 静态内部类
    static class SingletonFactory {
        private final static Singleton instance = new Singleton();
    }
}
```

#### 3.0 单例应用

&emsp;单例模式应用包括：

- 数据库连接池
- 唯一生成ID

