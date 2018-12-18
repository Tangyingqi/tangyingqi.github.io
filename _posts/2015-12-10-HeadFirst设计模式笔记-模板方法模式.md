---
layout: post
title: 'HeadFirst设计模式笔记-模板方法模式'
tags: [code]
---

### 什么是模板方法模式

定义：在一个方法中定义一个算法的骨架，而将一些步骤延迟到子类中。模板方法是的子类可以在不改变算法结构的情况下，重新定义算法中的某些步骤。

简单的说，就是把一些共有的方法放在一个抽象类中，这个类中定义了一些共有的方法，这些方法子类不必去实现，还有一些定义为抽象方法，这些方法是各子类有所差异的地方，所以要子类自己去覆写。有一些方法，被称为钩子，它不被定义成抽象方法，子类可以选择是否覆写它。它的作用是可以改变父类的一些行为。


### 举个例子

煮茶和煮咖啡的步骤基本是重复的，只是有些具体实现不同。这时就可以用到模式方法模式。

下面我们先写出总的抽象类

	public abstract class CaffeineBeverage {
	void prepareRecipe(){
		boilWater(); //煮水
		brew();      //酿造
		pourInCup(); //倒进杯子中
		if(customerWantsCondiments()){
			addCondiments();//添加调料
		}
	}
	abstract void brew();
	abstract void addCondiments();
	
	void boilWater(){
		System.out.println("Boiling water");
	}
	void pourInCup(){
		System.out.println("Pouring into cup");
	}
	boolean customerWantsCondiments(){//“钩子”
		return true;
	}
	}	

可以看到，上面的一些方法可以在煮咖啡和茶之间通用，所以直接写好了。还有一些定义为抽象，让子类自己去实现。这个抽象类中还定义了“钩子”方法，它可以在子类中重写，然后影响这个类，是否加调料。

下面是咖啡类

	public class Coffee extends CaffeineBeverage{

	@Override
	void addCondiments() {
		System.out.println("Adding Sugar ang Milk");
	}
	@Override
	void brew() {
		System.out.println("Driping Coffee through filter");
	}
	@Override
	boolean customerWantsCondiments() {
		String answer = getUserInput();
		if(answer.toLowerCase().startsWith("y")){
			return true;
		}
		return false;
	}
	public String getUserInput(){
		String answer = null;
		System.out.println("Would you like add Condiments?(y/n)");
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		try {
			answer = in.readLine();
			
		} catch (IOException e) {
			e.printStackTrace();
		}
		if(answer==null){
			return "no";
		}
		return answer;
		
	}
	}

下面是茶类

	public class Tea extends CaffeineBeverage{

	@Override
	void addCondiments() {
		System.out.println("add some condiments");
	}

	@Override
	void brew() {
		System.out.println("Driping Tea through filter");
	}
	@Override
	boolean customerWantsCondiments() {
		String answer = getUserInput();
		if(answer.toLowerCase().startsWith("y")){
			return true;
		}
		return false;
	}

	private String getUserInput() {
		String answer = null;
		System.out.println("Would you like some condiments?(y/n)");
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		try {
			answer = in.readLine();
		} catch (IOException e) {
			e.printStackTrace();
		}
		if(answer==null){
			return "no";
		}
		return answer;
	}
	}

测试类

	public class Test {
	public static void main(String[] args) {
		Coffee coffee = new Coffee();
		System.out.println("making coffee...");
		coffee.prepareRecipe();
		
		Tea tea = new Tea();
		System.out.println("making tea...");
		tea.prepareRecipe();
	}
	}
结果：

	making coffee...
	Boiling water
	Driping Coffee through filter
	Pouring into cup
	Would you like add Condiments?(y/n)
	n
	making tea...
	Boiling water
	Driping Tea through filter
	Pouring into cup
	Would you like some condiments?(y/n)
	y
	add some condiments

上面的子类都复写了“钩子方法”，让用户输入，以影响父类的方法。

### 现实中的模板方法模式

Arrays.sort() 这个系统提供的方法就利用了模板方法模式，现实中的模式往往不容易看出来。sort方法是静态的，这样所有数组都可以利用它排序，它需要实现Comparable接口并重写其中的 compareTo（）方法，否则无法排序。

	public class Duck implements Comparable {

	String name;
	int weight;

	public Duck(String name, int weight) {
		this.name = name;
		this.weight = weight;
	}

	public String toString() {
		return name + " weights " + weight;
	}

	@Override
	public int compareTo(Object o) {
		Duck otherDuck = (Duck) o;
		if (this.weight < otherDuck.weight) {
			return -1;
		} else if (this.weight == otherDuck.weight) {
			return 0;
		}else{
			return 1;
		}

	}
	}
这个compareTo方法就相当于上面的“钩子”，它需要子类去实现，按照子类的需求进行排序。

	public class DuckSortTest {
	public static void main(String[] args) {
		Duck[] ducks = {
				new Duck("Daffy",8),
				new Duck("Tom",15),
				new Duck("Huey",6),
				new Duck("Howard",2)
		};
		System.out.println("before sorting:");
		display(ducks);
		
		Arrays.sort(ducks);
		
		System.out.println("after sorting:");
		display(ducks);
	}
	public static void display(Duck[] ducks){
		for (int i = 0; i < ducks.length; i++) {
			System.out.println(ducks[i]);
		}
	}
	}
结果：

	before sorting:
	Daffy weights 8
	Tom weights 15
	Huey weights 6
	Howard weights 2
	
	after sorting:
	Howard weights 2
	Huey weights 6
	Daffy weights 8
	Tom weights 15

### 总结

- 模板方法模式就是一个父类里面定义一些子类中可以通用的方法，子类不能通用的部分交给子类去覆写，然后反过来影响父类中的方法。

	
- 模板方法的抽象类可以定义具体方法，抽象方法和钩子。抽象方法由子类实现。钩子是一种方法，它在抽象类中不做事，或者只做默认的事，子类可以选择要不要去覆盖它。

- 为了防止子类改变模板方法，可以将模板方法声明为final.

- 策略模式和模板方法模式都封装算法，一个用组合，一个用继承。

- 工厂方法是模板方法的一种特殊版本。