---
layout:     post
title:      "设计模式：装饰模式"
subtitle:   "装饰模式"
date:       2016--08-21 15:40:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式
---

## 设计原则
  * 开放关闭原则：类应该对扩展开放，对修改关闭。

## 定义
动态地将责任附加到对象上。若要扩展功能，装饰者提供了比继承更有弹性的替代方案。

## 类图
![](/img/in-post/post-2016-08-21/3.png)

星巴克咖啡示例：
![](/img/in-post/post-2016-08-21/4.png)

## 代码
{% highlight ruby %}
装饰者与被装饰者的接口：
public abstract class Beverage {
  public String description;
  public String getDescription(){
    return description;
  }
  public abstract double cost();
}
被装饰类：
public class Coffee extends Beverage {
 
  private int size;
  public Coffee(){
    description = "Coffee";
  }
 
  @Override
  public double cost() {
    return 1.9;
  }
}
抽象装饰类：
public abstract class Decorator extends Beverage {
  //所有的装饰者都必须重新实现cost()和getDescription()。
  @Override
  public abstract double cost();
  @Override
  public abstract String getDescription();
}
具体装饰者：
public class Mocha extends Decorator {
  private Beverage beverage;
  public Mocha(Beverage beverage){
    description = "Mocha";
    this.beverage = beverage;
  }
  @Override
  public String getDescription(){
    return description+"," + beverage.getDescription();
  }
  @Override
  public double cost() {
    return 0.5 + beverage.cost();
  }
}
 
public class Whip extends Decorator {
  private Beverage beverage;
 
  public Whip(Beverage beverage){
    description = "Whip";
    this.beverage = beverage;
  }
  @Override
  public String getDescription(){
    return description+"," + beverage.getDescription();
  }
 
  @Override
  public double cost() {
    return 0.5 + beverage.cost();
  }
}
//测试类：
public class App {
 
  public static void main(String[] args) {
    Coffee coffee = new Coffee();
    Mocha mocha = new Mocha(coffee);
    Whip whip = new Whip(mocha);
    System.out.println(whip.getDescription());
    System.out.println(whip.cost());
  }
}

{% endhighlight %}
