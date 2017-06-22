---
layout: post
title:  "Singleton pattern"
date:   2016-10-09 23:00:00
categories: Algorithm
tags: Algorithm
comments: false
---
* content
{:toc}

题目：请手写代码实现单例模式，要求多种实现方式，并阐述原理。
 <!--more-->

-----
**思考：**
单例模式是日常开发中一种非常常见的设计模式。在所有需要资源复用，或者保持引用实例唯一性的场景下，该设计模式都可以被运用来完成具体的实现。在ASP.NET中的HttpApplication，JAVA线程池，Winform的窗体等，都运用了单例模式。


单例模式在实现单例的过程中，本质上都是对以下几个问题的合理处理：保持单例唯一，控制获取实例的方法使用，保证线程安全，性能的瓶颈。在其中的一些点上，C#和JAVA的处理机制有些许不同，这里分别用两种语言进行实现。

本人的实现方式参考了一些前辈的博文，同时自己也整理了一下。仅仅作为参考，随时欢迎交流。

-----
## C#单例模式的实现


```
//1.经典模式：直接通过内部成员对象实现。问题：线程不安全，多线程并发的场景下无法保证数据可靠性。

    public class SingletonDemo1
    {
        private static SingletonDemo1 singleton;

        private SingletonDemo1()
        {
        }

        public static SingletonDemo1 getInstance()
        {
            if (singleton == null)
            {

                singleton = new SingletonDemo1();
            }
            return singleton;
        }
    }

//2.懒汉模式：通过加锁实现线程安全。问题：该方法需要手动加锁，编码繁琐容易出错，且在并发的过程中线程是阻塞的，性能很差。
    public class SingletonDemo2
    {
        private volatile static SingletonDemo2 singleton = null;
        private static readonly object lockHelper = new object();
        private SingletonDemo2() { }
        public static SingletonDemo2 CreateInstance()
        {
            if (singleton == null)
            {
                lock (lockHelper)
                {
                    if (singleton == null)
                        singleton = new SingletonDemo2();
                }
            }
            return singleton;
        }
    }

//3.饿汉模式：通过在类内部主动创建实例实现。
    public class SingletonDemo3
    {
        private static readonly SingletonDemo3 singleton  
        = new SingletonDemo3();
        private SingletonDemo3() { }
        public static SingletonDemo3 CreateInstance()
        {
            return singleton;
        }
    }

//4.直接通过readonly修饰的成员获取实例。本质上和第三种方法是一样的，但是写起来很简洁。
    public class SingletonDemo4
    {
        private SingletonDemo4() { }
        public static readonly SingletonDemo4 singleton  
        = new SingletonDemo4();
    }

```

-----
## JAVA单例模式的实现

```
//1.懒汉模式，线程不安全。
    public class Singleton1 {  
        private static Singleton1 instance;  
        private Singleton1 (){}  

        public static Singleton1 getInstance() {  
        if (instance == null) {  
            instance = new Singleton1();  
        }  
        return instance;  
        }  
    }  

//2.懒汉模式，加锁实现线程安全。
    public class Singleton2 {  
        private static Singleton2 instance;  
        private Singleton2 (){}  
        public static synchronized Singleton2 getInstance() {  
        if (instance == null) {  
            instance = new Singleton2();  
        }  
        return instance;  
        }  
    }  
//3.饿汉模式
    public class Singleton3 {  
        private static Singleton3 instance = new Singleton3();  
        private Singleton3 (){}  
        public static Singleton3 getInstance() {  
        return instance;  
        }  
    }  
//4.饿汉模式，通过static关键字修饰，同样是在初始化即完成实例化。
    public class Singleton4 {  
        private Singleton4 instance = null;  
        static {  
        instance = new Singleton4();  
        }  
        private Singleton4 (){}  
        public static Singleton4 getInstance() {  
        return this.instance;  
        }  
    }  
//5.通过内部静态类
    public class Singleton5 {  
        private static class SingletonHolder {  
        private static final Singleton5 INSTANCE = new Singleton5();  
        }  
        private Singleton5 (){}  
        public static final Singleton5 getInstance() {  
        return SingletonHolder.INSTANCE;  
        }  
    }  
//6.通过枚举实现。
    public enum Singleton6 {  
        INSTANCE;  
	/***
	Define other members of the enumerator.
	***/  
    }  
//7.双重锁校验，此种方法类似c#中的lock方式，对判定和实例化对象的过程加锁来实现。
    public class Singleton7 {  
        private volatile static Singleton7 singleton;  
        private Singleton7 (){}  
        public static Singleton7 getSingleton() {  
        if (singleton == null) {  
            synchronized (Singleton7.class) {  
            if (singleton == null) {  
                singleton = new Singleton7();  
            }  
            }  
        }  
        return singleton;  
        }  
    }  
```
