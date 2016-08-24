---
layout:     post
title:      "设计模式：模板方法模式"
subtitle:   "模板方法模式"
date:       2016--08-24 17:00:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式

---

## 定义
模板方法模式在一个方法中定义了一个算法的骨架，而将一些步骤延迟到子类中。模板方法是的i之类可以在不改变算法结构的情况下，重新定义算法中的某些步骤。
模板方法定义了一个算法的步骤，并允许子类为一个或多个步骤提供实现。
模板就是一个方法，这个方法将算法定义成一组步骤，其中的任何步骤都可以是抽象的，由子类负责实现。

## 类图
![](/img/in-post/post-2016-08-24/1.png)

抽象类AbstractClass包含了模板方法，这个模板方法用到的操作的抽象版本为primitiveOperation1()和primitiveOperation2()。
模板方法在实现算法的过程中用到了两个原语操作。模板方法本身和这两个操作的具体实现被解耦了。
可能有许多具体类，每一个都实现了模板方法所需的全部操作。

## 代码
{% highlight ruby %}
//模板方法的抽象类
public abstract class CaffeineBeverageWithHook {
    public void prepareRecipe(){
        boilWater();
        brew();
        if(customerWantsCondiments()){
            addCondiments();
        }
        pourInCup();
    }
 
    public abstract void brew();
    public abstract void addCondiments();
    //钩子函数 
    public boolean customerWantsCondiments(){
        return false;
    }
    public void boilWater() {
        System.out.println("Boil water...");
    }
    public void pourInCup(){
        System.out.println("pour into cup...");
    }
}

具体类
public class CoffeeWithHook extends CaffeineBeverageWithHook {
 
    @Override
    public void brew() {
        System.out.println("brew coffee!");
    }
 
    @Override
    public void addCondiments() {
        System.out.println("add milk and sugar!");
    }
 
    public boolean customerWantsCondiments(){
        boolean b = false;
        System.out.println("Do you want add milk and sugar?(y/n)");
        Scanner scan = new Scanner(System.in);
        String answer = scan.next();
        if("y".equals(answer) || "Y".equals(answer))
            b = true;
        return b;
    }
}
测试类
public class Test {
    public static void main(String[] args) {
        CoffeeWithHook coffeeWithHook = new CoffeeWithHook();
        coffeeWithHook.prepareRecipe();
    }
}
{% endhighlight %}

## 设计原则
好莱坞原则：别吊用我们，我们会调用你。
好莱坞原则可以防止“依赖腐败”。当高层组件依赖低层组件，低层组件又依赖高层组件，而高层组件又依赖边侧组件，而边侧组件又依赖低层组件时，依赖腐败就发生了。
在好莱坞原则下，我们允许底层组件将自己挂钩到系统上，但是高层组件会决定什么时候和怎样使用这些低层组件。该原则将决策权放在高层模块中，以便决定如何以及何时调用低层模块。

## 要点

钩子
钩子是一种被声明在抽象类中的方法，但只有空的或者默认的实现。
钩子的存在让子类有能力对算法的不同点进行挂钩，要不要挂钩由子类决定，挂钩即由子类覆盖钩子方法。

为了防止子类改变模板方法中的算法，可以将模板方法声明为final。

工厂方法时模板方法的一个特殊变体

策略模式定义了一个算法家族，并让这些算法可以互换。正因为每一个算法都被封装起来了，所以客户可以轻易地使用不同的算法。策略模式不是通过继承实现算法，而是通过对象组合的方式，让客户可以选择算法实现。

模板方法定义了一个算法大纲，由子类定义其中的某些步骤的内容。这样使得算法中的个别步骤可以有不同的细节，但是算法的结构依然维持不变。
