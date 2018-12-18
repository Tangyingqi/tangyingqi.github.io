---
layout: post
title: 'HeadFirst设计模式笔记-工厂模式'
tags: [code]
---

# 简单工厂模式

简单工厂模式不是设计模式，它是工厂模式的基础。了解简单工厂模式，对理解工厂模式有帮助。

### 什么是简单工厂模式

工厂模式就是把创建对象的工作封装成一个单独的类，你可以通过工厂得到你需要的对象。

### 举个例子

一家批萨店，有不同口味的批萨，通过一个简单工厂类封装创建对象的工作。

首先需要抽象出一个pizza超类

	public abstract class Pizza {
		abstract void display();
	}

创建两种Pizza类继承超类

	public class CheesePizza extends Pizza{

	@Override
	void display() {
		System.out.println("make a CheesePizza");
	}
	}

	public class PepperoniPizza extends Pizza{

	@Override
	void display() {
		System.out.println("make a PepperoniPizza");
	}
	}

创建一个简单工厂类

	public class SimplePizzaFactory {

	public Pizza createPizza(String type){
		Pizza pizza = null;
		if(type.equals("cheese")){
			pizza = new CheesePizza();
		}else if(type.equals("pepperoni")){
			pizza = new PepperoniPizza();
		}
		return pizza;
	}
	}
现在可以生产pizza啦

	public class MakePizza {
	public static void main(String[] args) {
		SimplePizzaFactory factory = new SimplePizzaFactory();
		Pizza pizza = factory.createPizza("pepperoni");
		pizza.display();
		Pizza pizza1 = factory.createPizza("cheese");
		pizza1.display();
	}
	}

结果：     
make a PepperoniPizza     
make a CheesePizza

# 工厂模式

### 什么是工厂模式，它跟简单工厂有什么区别

同样，工厂模式的整体思想就是封装创建对象的工作，但是不同于简单工厂模式，它的工厂是抽象的，也就是说可以有其他工厂加入进来。而且工厂中会带有抽象的工厂方法，它的实现由子类去实现，返回给它对象。这样程序就更加灵活了，因为，你不知道该创建什么对象，而子类最清楚(或者说消费者最清楚)，那就交给子类去做。

上面的例子，虽然做到了加不同的东西，做出不同的批萨。但是现实情况是，加同样的东西，不同的地方的口味也不一样。这该怎么做呢？显然上面的例子不容易扩展了。

看下面这个例子，它加入了纽约风味，为了减少代码量就加这一个了。

首先我们看工厂类发生了什么变化

	public abstract class PizzaStore {//变成了抽象方法

	public Pizza orderPizza(String type){
		Pizza pizza;
		pizza = createPizza(type);
		
		return pizza;
	}
	abstract Pizza createPizza(String type);//这个抽象方法是工厂方法，让子类去实现
	}

然后出现类纽约的工厂类继承上面的工厂类，来制作纽约风味的批萨。

	public class NYStylePizzaStore extends PizzaStore{

	@Override
	Pizza createPizza(String type) {
		if(type.equals("cheese")){
			return new NYStyleCheesePizza();
		}else if(type.equals("pepperoni")){
			return new NYStylePepperoniPizza();
		}
		return null;
	}
	}

纽约工厂提供了两种批萨，分别是

	public class NYStyleCheesePizza extends Pizza{

	@Override
	void display() {
		System.out.println("NYStyle and Cheese Pizza");
	}
	}

	public class NYStylePepperoniPizza extends Pizza{

	@Override
	void display() {
		System.out.println("NyStyle and Pepperoni Pizza");
	}
	}

下面就可以开始制作了

	public class MakePizzaTest {
	public static void main(String[] args) {
		PizzaStore nyPizzaStore = new NYStylePizzaStore();
		Pizza pizza = nyPizzaStore.orderPizza("cheese");
		pizza.display();
	}
	}
结果是：
     
	NYStyle and Cheese Pizza

# 抽象工厂模式

### 抽象工厂和工厂模式的区别

抽象工厂模式的定义：为创建一组相关或相互依赖的对象提供一个接口，而且无需指定他们的具体类。

它们的工作都是创建对象，工厂模式是用继承，而抽象工厂是通过对象的组合。

抽象工厂提供一个用来创建一个产品家族的抽象类型，这个类型的子类定义了产品被产生的方法。

产品家族：比如苹果公司就是一个产品家族，它不光有手机，还有电脑，平板一系列的产品。

### 举个例子

	 public interface Apple{
          
		void AppleStyle();
     }

	public interface Sumsung{

         void SumsungStyle();
     }

	public class iPhone implements Apple{

	@Override
	public void AppleStyle() {
		System.out.println("this is AppleStyle phone");
	}
	}

	public class iPad implements Apple{

	@Override
	public void AppleStyle() {
		System.out.println("This is Apple pad");
	}
	}

	public class Note implements Sumsung{

	@Override
	public void SumsungStyle() {
		System.out.println("This is Sumsung phone");
	}
	}

	public class SumsungPad implements Sumsung{

	@Override
	public void SumsungStyle() {
		System.out.println("This is Sumsung pad");
	}
	}

	public interface Factory {//工厂方法定义为接口
		Apple createAppleProduct();
		Sumsung createSumsungProduct();
	}
	//手机工厂
	public class Factory_phone implements Factory{

	@Override
	public Apple createAppleProduct() {
		return new iPhone();
	}

	@Override
	public Sumsung createSumsungProduct() {
		return new Note();
	}
	}
	//平板工厂
	public class Factory_Pad implements Factory{

	@Override
	public Apple createAppleProduct() {
		return new iPad();
	}

	@Override
	public Sumsung createSumsungProduct() {
		return new SumsungPad();
	}
	}

测试类

	public class Test {
	public static void main(String[] args) {
		Factory factory = new Factory_phone();
		Apple phone = factory.createAppleProduct();
		phone.AppleStyle();
		
		Factory factory1 = new Factory_Pad();
		Sumsung pad = factory1.createSumsungProduct();
		pad.SumsungStyle();
	}
	}	
结果：    

	this is AppleStyle phone
	This is Sumsung pad


# 总结

工厂模式有两种，普通工厂模式和抽象工厂模式。它们的工作都是一样的，只是抽象工厂模式服务于更大型的需求。利用接口实现，对象组合。缺点是想要添加新的种类，就要改接口中的内容。需要改的代码很多。

一句话总结工厂模式就是，使用抽象类中的抽象方法，让子类自己去实现创建对象。