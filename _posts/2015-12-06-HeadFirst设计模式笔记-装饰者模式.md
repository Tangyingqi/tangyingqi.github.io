---
layout: post
title: 'HeadFirst设计模式笔记-装饰者模式'
tags: [code]
---

### 装饰者模式定义
装饰者模式是相对比较容易理解的一种模式，因为我们在Java，IO流的部分经常会用到，BufferedInputStream（FileInputStream）就是一个装饰者模式。     

主要由组件（components）和装饰器（Decorator）组成，组件被装饰器装饰。

- 装饰者和被装饰对象有相同的超类型。
- 你可以用一个或多个装饰者包装一个对象。
- 既然装饰者和被装饰者拥有相同超类型，所以在任何需要原始对象(被包装的)的场合，可以用装饰过的对象代替它。
- 装饰者可以在所委托被装饰者的行为之前或之后，加上自己的行为，以达到特定的目的。
- 对象可以在任何时候被装饰，所以可以在运行时动态地、不限量地用你喜欢的装饰者来装饰对象。

### 举例说明

一个咖啡店，它有一些原味的咖啡，但是顾客的需求是复杂的，顾客会要求加一些奶泡、摩卡之类的，顾客的需求千变万化，加不同的的东西价格就会不一样，所以要灵活的设计。下面这个例子，我们会有两种类型的咖啡，两种调料。调料是装饰者，咖啡是被装饰者。

首先，我们定义一个饮料的抽象类，实现一些共有方法。

	public abstract class Beverage {
	String description = "unKnow Beverage";
	public String getDescription(){
		return description;
	}
	public abstract double cost();
	}

然后我们需要一个调料装饰者的抽象类，这个类继承上面的饮料抽象类。目的是，装饰者和被装饰对象有相同的超类型。

	public abstract class CondimentDecorator extends Beverage{
	public abstract String getDescription();
	}

下面是第一种类型的咖啡

	public class Espresso extends Beverage{

	public Espresso() {
		description = "Espresso";
	}
	@Override
	public double cost() {
		return 1.99;
	}
	}

下面是第二种类型的咖啡

	public class HouseBlend extends Beverage{

	public HouseBlend() {
		description = "HouseBlend";
	}
	@Override
	public double cost() {
		return 1.89;
	}
	}

我们定义两中调料，也就是装饰者。下面是摩卡调料

	public class Mocha extends CondimentDecorator{

	Beverage beverage;
	public Mocha(Beverage beverage) {
		this.beverage = beverage;
	}
	@Override
	public String getDescription() {
		return beverage.getDescription()+",Mocha";
	}

	@Override
	public double cost() {
		return 0.2+beverage.cost();
	}
	}

下面是第二种，奶泡调料

	public class Whip extends CondimentDecorator{
	
	Beverage beverage;
	public Whip(Beverage beverage) {
		this.beverage = beverage;
	}
	@Override
	public String getDescription() {
		return beverage.getDescription()+",Whip";
	}

	@Override
	public double cost() {
		return 1.2+beverage.cost();
	}
	}

装饰者和被装饰者都到齐了，下面我们可以开始使用装饰者模式了。

	public class Coffee {
	public static void main(String[] args) {
		Beverage beverage = new Espresso();
		System.out.println(beverage.getDescription()+" $"+beverage.cost());
		
		Beverage beverage2 = new HouseBlend();
		beverage2 = new Mocha(beverage2);
		beverage2 = new Whip(beverage2);
		System.out.println(beverage2.getDescription()+" $"+beverage2.cost());
	}
	}

结果是：      
Espresso $1.99     
HouseBlend,Mocha,Whip $3.29

beverage2运用了装饰者模式，首先创建一种咖啡，然后放入Mocha，然后再一起放入Whip里，这样一种咖啡加两种调料就融合起来了。   
![current](http://i12.tietuku.com/0c41d07cbb84eb40.png)      

此时它们是这样的。

### 总结

装饰者模式首先有一个超类，是装饰者和被装饰者都继承的，在这个基础上，它们才能利用多态层层嵌套，每一层都是一个装饰者，它们会实现自己的方法为被装饰者添加上一些功能，例如，IO里面，为InputStream添加缓冲。     
在这里继承不是为了获得行为，而是为了有共同的超类型。