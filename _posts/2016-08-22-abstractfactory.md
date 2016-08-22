---
layout:     post
title:      "设计模式：抽象工厂"
subtitle:   "抽象工厂"
date:       2016--08-22 12:00:00
author:     "Lushun"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 设计模式

---

# 原则

# 定义
提供了一个接口，用于创建相关或依赖对象的家族，而不需要明确的指定具体类。


# 类图
![](/img/in-post/post-2016-08-22/1.png)


AbstractFactory
抽象工厂定义了一个接口，所有具体工厂都必须实现此接口，这个接口包含一组方法来生产产品。
Concrete Factory
每个具体工厂实现不同的产品家族。要创建一个产品，客户只要使用其中的一个工厂而完全不需要实例化任何产品对象。
AbstractProduct
产品家族。每个工厂都能生产一整组的产品。
客户的代码只需涉及抽象工厂，运行时将自动使用实际工厂。
下面是这个例子的抽象工厂方法类图：
![](/img/in-post/post-2016-08-22/2.png)


# 工厂方法与抽象工厂
工厂方法使用继承，抽象工厂通过对象的组合。  

工厂方法通过子类来创建对象，客户只需要知道它们所使用的抽象类型就可以，而子类负责决定具体类型，只负责将客户从具体类型中解耦。  

抽象工厂创建一个产品家族的抽象类型，这个类型的子类定义了产品被产生的方法。要使用这个工厂必须先实例化它，然后将它传入一些针对抽象类型所写的代码中。（在这个例子中就是将NYIngredientFactory这样的类传入CheesePizza这样的类中。NYIngredientFactory是IngredientFactory的子类，CheesePizza是Pizza的子类。）  

抽象工厂的具体工厂经常实现工厂方法来创建它们的产品，不过这些具体工厂纯粹只是用来创建产品罢了，具体的使用则是将工厂自己传入其他类中，在其他类中生产产品。而工厂方法的抽象创建者（Creator）中所实现的代码通常会用到子类所创建的具体类型。  
当你需要创建产品家族和想让制造的相关产品集合起来时，使用抽象工厂。  
工厂方法可以把你的客户代码从需要实例化的具体类中解耦，或者如果你目前还不知道将来需要实例化哪些具体类时，可以使用工厂方法。只要继承成子类，并实现工厂方法即可。  

# 代码
接下来的例子结合了工厂方法和抽象工厂两种模式。
首先是工厂方法部分：
类图依旧是前一个工厂方法模式中的类图：
![](/img/in-post/post-2016-08-22/3.jpg)

前面工厂方法例子的代码做了一些改变来整合后面的抽象工厂。
PizzaStore：
{% highlight ruby %}
//定义了工厂方法的抽象类
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

//接下来是两个具体工厂类：
//芝加哥风味的store
public class ChgPizzaStore extends PizzaStore {
 
    @Override
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        PizzaIngredientFactory pizzaIngredientFactory = new ChgPizzaIngredientFactory();
        if(type.equals("cheese")){
            pizza = new CheesePizza(pizzaIngredientFactory);
            pizza.setName("Chg tyle cheese pizza");
        }else if(type.equals("clam")){
            pizza = new ClamsPizza(pizzaIngredientFactory);
            pizza.setName("Chg tyle clam pizza");
        }
        return pizza;
    }
}
//NYPizzaStore：纽约风味的store
public class NYPizzaStore extends PizzaStore {
 
    @Override
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        PizzaIngredientFactory pizzaIngredientFactory = new NYPizzaIngredientFactory();
        if(type.equals("cheese")){
            pizza = new CheesePizza(pizzaIngredientFactory);
            pizza.setName("NY tyle cheese pizza");
        }else if(type.equals("clam")){
            pizza = new ClamsPizza(pizzaIngredientFactory);
            pizza.setName("NY tyle clam pizza");
        }
        return pizza;
    }
}
{% endhighlight %}
可以看到具体工厂里都有一个PizzaIngredientFactory的抽象类。它就是抽象工厂的抽象类。
这两个具体Store类是工厂方法的具体工厂类，同时也是抽象工厂的客户，使用抽象工厂的工厂类来生产产品。

{% highlight ruby %}
//PizzaIngredientFactory：抽象工厂的抽象类，定义了生产一整套产品的抽象方法。
public interface PizzaIngredientFactory { 
    public Dough createDough();
    public Sauce createSauce();
    public Cheese createCheese();
    public Clams createClams();
}
//下面是两个具体工厂类，实现抽象工厂类中的方法来说明具体如何生产每种产品
//这是纽约风味的工厂：
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
 
    @Override
    public Dough createDough() {
        System.out.println("creating NYDough");
        return new NYDough();
    }
 
    @Override
    public Sauce createSauce() {
        System.out.println("creating NYSauce");
        return new NYSauce();
    }
 
    @Override
    public Cheese createCheese() {
        System.out.println("creating NYCheese");
        return new NYCheese();
    }
 
    @Override
    public Clams createClams() {
        System.out.println("creating NYClams");
        return new NYClams();
    }
}
//芝加哥风味的工厂：
public class ChgPizzaIngredientFactory implements PizzaIngredientFactory {
 
    @Override
    public Dough createDough() {
        System.out.println("creating ChgDough...");
        return new ChgDough();
    }
 
    @Override
    public Sauce createSauce() {
        System.out.println("creating ChgSauce...");
        return new ChgSauce();
    }
 
    @Override
    public Cheese createCheese() {
        System.out.println("creating ChgCheese...");
        return new ChgCheese();
    }
 
    @Override
    public Clams createClams() {
        System.out.println("creating ChgClams...");
        return new ChgClams();
    }
}
这个抽象工厂模式例子中的一整组产品就是下面几种类型的食物：
//Dough
public interface Dough {
}
//以及它的两种风格的实现：
public class NYDough implements Dough {
    public NYDough(){
    }
}
public class ChgDough implements Dough {
    public ChgDough(){
    }
}
//Sauce
public interface Sauce {
}
public class ChgSauce implements Sauce {
}
public class NYSauce implements Sauce {
}
//Cheese
public interface Cheese {
}
public class NYCheese implements Cheese {
}
public class ChgCheese implements Cheese {
}
//Clams
public interface Clams {
}
public class NYClams implements Clams {
}
public class ChgClams implements Clams {
}
{% endhighlight %}

前面说了：  

    抽象工厂创建一个产品家族的抽象类型，这个类型的子类定义了产品被产生的方法。要使用这个工厂必须先实例化它，
    然后将它传入一些针对抽象类型所写的代码中。

这里说的将它传入一些针对抽象类型所写的代码中，就是抽象工厂的使用方式，仅仅负责生产产品，并不会使用产品，通常将自己传入其他类中，有其他类调用来生产产品。这一点不同于工厂方法，工厂方法的工厂类中一般会使用工厂方法生产的产品。


这个例子中使用（组合）这些产品类的是Pizza类：
{% highlight ruby %}
//Pizza
public abstract class Pizza {
    String name;
    Dough dough;
    Sauce sauce;
    Clams clams;
    Cheese cheese;
 
    public abstract void prepare();

    public void bake(){
        System.out.println("bake...");
    }
    public void cut(){
        System.out.println("cut...");
    }
    public void box(){
        System.out.println("box...");
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return name;
    }
}

//最后是测试类：
public class App { 
    public static void main(String[] args) {
        PizzaStore nyPizzaStore = new NYPizzaStore();
        Pizza pizza = nyPizzaStore.orderPizza("clam");
        System.out.println(pizza);

        PizzaStore chgPizzaStore = new ChgPizzaStore();
        Pizza pizza2 = chgPizzaStore.orderPizza("cheese");
        System.out.println(pizza2);
    }
}

//测试结果：
creating NYCheese
creating NYClams
creating NYDough
creating NYSauce
bake...
cut...
box...
NY tyle clam pizza
creating ChgCheese...
creating ChgClams...
creating ChgDough...
creating ChgSauce...
bake...
cut...
box...
Chg tyle cheese pizza
{% endhighlight %}
