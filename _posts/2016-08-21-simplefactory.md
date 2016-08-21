---
layout:     post
title:      "设计模式：简单工厂和工厂方法"
subtitle:   "简单工厂和工厂方法"
date:       2016--08-21 15:40:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式
---

# 简单工厂

## 定义
简单工厂其实不是一个设计模式，反而更像是一种编程习惯。

## 类图
![](/img/in-post/post-2016-08-21/5.png)

## 代码
{% highlight ruby %}
	//简单工厂：
	//客户类：
	public class PizzaStore {
	 
		SimplePizzaFactory simplePizzaFactory;
	 
		public PizzaStore(SimplePizzaFactory simplePizzaFactory){
			this.simplePizzaFactory = simplePizzaFactory;
		}
		public Pizza orderPizza(String type){
			Pizza pizza = null;
			pizza = simplePizzaFactory.createPizza(type);
			pizza.prepare();
			pizza.bake();
			pizza.cut();
			pizza.box();
			return pizza;
		}
	}
	 
	public class SimplePizzaFactory {
	 
		public Pizza createPizza(String type){
			Pizza pizza = null;
			if(type.equals("cheese"))
				pizza = new CheesePizza();
			else if(type.equals("clam"))
				pizza = new ClamPizza();
			else if(type.equals("veggie"))
				pizza = new VeggiePizza();
			return pizza;
		}
	}
	抽象产品类：
	public abstract class Pizza {
		String description;
		public String getDescription(){
			return description;
		}
		public void prepare(){
			System.out.println("prepare...");
		}
		public void bake(){
			System.out.println("bake...");
		}
		public void cut(){
			System.out.println("cut...");
		}
		public void box(){
			System.out.println("box...");
		}
	}
	具体产品类：
	public class CheesePizza extends Pizza {
	 
		public CheesePizza(){
			this.description = "CheesePizza";
		}
	}
	public class ClamPizza extends Pizza {
	 
		public ClamPizza(){
			this.description = "ClamPizza";
		}
	}
	public class VeggiePizza extends Pizza {
	 
		public VeggiePizza(){
			this.description = "VeggiePizza";
		}
	}
	测试类：
	public class App {
	 
		public static void main(String[] args) {
			PizzaStore store = new PizzaStore(new SimplePizzaFactory());
			Pizza pizza = store.orderPizza("cheese");
			System.out.println(pizza.getDescription());
		}
	}
{% endhighlight %}

# 工厂方法

## 设计原则
* 依赖倒置原则  
要依赖抽象，不要依赖具体类。这个原则听起来很像“针对接口编程，不针对实现编程”，但这个原则更强调“抽象”。说明了：不能让高层组件依赖底层组件，而且不管高层组件或底层组件，都应该依赖于“抽象”。
![](/img/in-post/post-2016-08-21/6.png)  
比如上面这个图：PizzaStore是高层组件，具体的Pizza是底层组件。PizzaStore依赖于具体Pizza类。
这个原则告诉我们，应该重写代码以便于我们依赖于抽象类，而不是具体类。对于高层即底层模块都应如此。

## 定义
简单工厂是由一个对象负责所有具体类的实例化，工厂方法由一群子类来负责实例化。通过让子类决定该创建的对象是什么，来达到将对象创建的过程封装的目的。  
官方定义：定义了一个创建对象的接口，但由子类决定要实例化的类是哪一个。工厂方法让类把实例化推迟到子类。

## 类图
![](/img/in-post/post-2016-08-21/7.png)  
抽象的Creator提供了一个创建对象的方法的接口，也称为“工厂方法”。在抽象的Creator中，任何其他实现的方法都可能使用到这个工厂方法制造出来的产品，但只有子类才真正实现这个工厂方法并创建产品。

## 代码
{% highlight ruby %}
含有工厂方法的工厂父类：
public abstract class PizzaStore {
  public Pizza orderPizza(String type){
    Pizza pizza = null;
    pizza = createPizza(type);
    pizza.prepare();
    pizza.bake();
    pizza.cut();
    pizza.box();
    return pizza;    
  }
  public abstract Pizza createPizza(String type);
}
实现工厂方法的工厂子类：
public class NYPizzaStore extends PizzaStore {
 
  @Override
  public Pizza createPizza(String type) {
    Pizza pizza = null;
    if(type.equals("cheese"))
      pizza = new NYStyleCheesePizza();
    else if(type.equals("clam"))
      pizza = new NYStyleClamPizza();
    return pizza;
  }
}
public class ChgPizzaStore extends PizzaStore {
 
  @Override
  public Pizza createPizza(String type) {
    Pizza pizza = null;
    if(type.equals("cheese"))
      pizza = new ChgStyleCheesePizza();
    else if(type.equals("clam"))
      pizza = new ChgStyleClamPizza();
    return pizza;
  }
}
 
所有产品的父类：
public abstract class Pizza {
  String description;
  public String getDescription(){
    return description;
  }
}
具体产品类：
public class NYStyleCheesePizza extends Pizza {
  public NYStyleCheesePizza(){
    description = "NYStyleCheesePizza";
  }
}
public class NYStyleClamPizza extends Pizza {
 
  public NYStyleClamPizza(){
    description = "NYStyleClamPizza";
  }
}
public class ChgStyleCheesePizza extends Pizza {
 
  public ChgStyleCheesePizza(){
    description = "ChgStyleCheesePizza";
  }
}
public class ChgStyleClamPizza extends Pizza {
 
  public ChgStyleClamPizza(){
    description = "ChgStyleClamPizza";
  }
}
测试类：
public class App {
  public static void main(String[] args) {
    PizzaStore nyPizzaStore = new NYPizzaStore();
    PizzaStore chgPizzaStore = new ChgPizzaStore();
    Pizza nycheese = nyPizzaStore.orderPizza("cheese");
    Pizza chgclam = chgPizzaStore.orderPizza("clam");
    System.out.println(nycheese.getDescription());
    System.out.println(chgclam.getDescription());
  }
}
{% endhighlight %}
