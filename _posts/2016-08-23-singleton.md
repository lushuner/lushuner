---
layout:     post
title:      "设计模式：单例模式"
subtitle:   "单例模式"
date:       2016--08-22 12:00:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式

---

## 定义
确保一个类只有一个实例，并提供一个全局访问点。

## 类图
![](/img/in-post/post-2016-08-23/1.png)

简单的设计模式。

## 代码
饿汉式：
{% highlight ruby %}
class SingletonHungry{
    private static SingletonHungry singletonHangry = new SingletonHungry();
    public static int c = 2;

    private SingletonHungry(){
        System.out.println("c="+ c);
    }
    public static SingletonHungry getInstance(){
        return singletonHangry;
    }
}
{% endhighlight %}

 根据上面代码分析类加载后过程：
 * 当类的class文件从磁盘加载到内存后，首先在堆区创建一个该类的Class对象，映射方法区该类的信息。
 * 然后为该类的static成员变量分配空间并为之赋该类型的默认值，并按照顺序执行static 代码块。
所以，上面的代码的构造器函数中输出“c=2";

如果代码改为：
{% highlight ruby %}
class SingletonHungry{
    public static int c = 2;
    private static SingletonHungry singletonHangry = new SingletonHungry();

    private SingletonHungry(){
        System.out.println("c="+ c);
    }
    public static SingletonHungry getInstance(){
        return singletonHangry;
    }
}
{% endhighlight %}
则构造器函数中输出“c=0”；

懒汉式：
{% highlight ruby %}
class SingletonLazy{
    private static SingletonLazy singletonLazy = null;
 
    public static SingletonLazy getInstance(){
        if(singletonLazy == null){
            singletonLazy = new SingletonLazy();
        }
        return singletonLazy;
    }
    private SingletonLazy(){
    }
}
{% endhighlight %}
## 线程安全性

### 饿汉式单例模式是线程安全的

饿汉式再静态初始化时创建了单例对象，因此保证了线程安全性。虚拟机再加载这个类时马上创建此唯一的单例，JVM保证任何线程访问singletonHangry 之前，一定先创建实例。

### “双重检查加锁"保证线程安全：

{% highlight ruby %}
public class Singleton {
    private volatile static Singleton singleton = null;
    private Singleton(){}
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized (Singleton.class) {
                singleton = new Singleton();
            }
        }
        return singleton;
    }
}
{% endhighlight %}
上面的懒汉式代码，只有在第一次创建实例时才同步，之后就不需要同步了。

## 要点

        单例模式确保程序中一个类最多只有一个实例

        单例模式提供访问这个实例的全局访问点。

        在Java中实现单例模式需要私有构造器，一个静态方法和一个静态变量。

        确定在性能和资源上的限制，然后小心的选择适当的方案来实现单例，以解决多线程的问题。

        如果使用多个类加载器，可能导致单例失效而产生多个实例。
