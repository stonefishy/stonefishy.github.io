---
title: 设计模式之单例模式详解与应用
date: 2017-05-19 20:31:18
tags: [单例模式]
categories: 设计模式
---

### 引言
在软件开发中，设计模式是一种解决常见问题的经验总结，它提供了一套可复用的解决方案，帮助开发人员更高效地编写可维护和可扩展的代码。单例模式是设计模式中的一种，它确保一个类只有一个实例，并提供一个全局访问点。

{% image /assets/images/design-pattern/singleton-pattern.webp, width=800px, alt=Singleton Pattern %}

### 单例模式的应用场景

单例模式适用于以下情况：

**资源共享的场景**： 在整个系统中，需要共享某个资源，例如配置信息、数据库连接池等。
**控制资源的访问**： 在多线程的情况下，避免多个线程同时访问某个资源，引起数据不一致。
**实例控制**： 某些类创建实例需要消耗较多资源，通过单例模式可以减少实例的创建，提高性能。

### 单例模式的实现
废话不多说，直接Show代码吧，这里演示下三种创建单例模式的代码

##### 1. 饿汉式单例模式

``` java 
public class Singleton {
    // 在类加载时就创建实例
    private static final Singleton instance = new Singleton();

    // 私有构造方法，防止外部实例化
    private Singleton() {
    }

    // 提供全局访问点
    public static Singleton getInstance() {
        return instance;
    }
}

```

##### 2. 懒汉式单例模式（双重检查锁）

``` java 
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {
        if (instance == null) { // 首次判断是否已经初始化过
            synchronized (Singleton.class) { // 加锁，一次只允许一个线程进入
                if (instance == null) { // 再次检查是否为空，因为 instance = new Singleton() 并非原子性操作
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

```

##### 3. 静态内部类单例模式

``` java 
public class Singleton {
    // 私有静态内部类
    private static class SingletonHolder {
        private static final Singleton instance = new Singleton();
    }

    private Singleton() {
    }

    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
}

```

### 单例模式的应用示例
假设我们有一个日志记录器，我们希望在整个应用程序中只有一个实例记录日志，可以使用单例模式来实现：

``` java 
public class Logger {
    private static Logger instance;

    private Logger() {
    }

    public static Logger getInstance() {
        if (instance == null) {
            synchronized (Logger.class) {
                if (instance == null) {
                    instance = new Logger();
                }
            }
        }
        return instance;
    }

    public void log(String message) {
        System.out.println("Log: " + message);
    }
}

```

在应用中，我们可以通过以下方式获取日志记录器的实例：

``` java 
Logger logger = Logger.getInstance();
logger.log("This is a log message.");

```

通过单例模式，确保了整个应用程序只有一个日志记录器实例，避免了资源浪费和数据不一致的问题。

### 总结
单例模式是一种简单而强大的设计模式，可以有效地解决特定问题，例如资源共享、实例控制等。在使用单例模式时，需要注意线程安全性，选择适合场景的实现方式。通过合理应用设计模式，我们能够编写出更加健壮和可维护的代码。