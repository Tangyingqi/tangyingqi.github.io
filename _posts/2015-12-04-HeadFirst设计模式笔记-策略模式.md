---
layout: post
title: 'HeadFirst设计模式笔记-策略模式'
tags: [code]
---

下面是我看Head first设计模式的笔记，以前没有看过设计模式相关的书，今天一看发现，设计模式能让你更好的理解关于面向对象，接口，继承，多态。后悔没有早点看，Head first 系列给人非常轻松的感觉，让枯燥的知识显得没那么枯燥，推荐。

我将通过书中的例子，进行梳理。这个例子涵盖了接口，继承，多态的使用，使程序更加灵活。提高代码复用性。利于程序的扩展。

这个例子是关于鸭子的一些行为，鸭子分很多种，这里我分为活鸭子和假鸭子，它们的共同点是都能浮在水上，不同点是，活鸭子叫声是呱呱的，且能飞，玩具鸭子的叫声是吱吱的，不能飞。还有一个不同点就是它们看起来是不一样的。

首先我们用一个接口，定义出飞的行为。

	public interface FlyBehavior {
	 	void fly();
	}
定义一个类“能飞” 实现飞的行为接口。

	public class FlyWithWings implements FlyBehavior{

	@Override
	public void fly() {
		System.out.println("i can fly");
	}

	}
定义一个类“不能飞” 实现飞的行为接口。

	public class FlyNoway implements FlyBehavior{

	@Override
	public void fly() {
		System.out.println("I can't fly,i am fake");
	}
	}
用一个接口定义叫的声音

	public interface QuackBehavior {
		void quack();
	}
定义一个类“呱呱叫” 实现叫的行为接口

	public class Quack implements QuackBehavior{

	@Override
	public void quack() {
		// TODO Auto-generated method stub
		System.out.println("guagua");
	}

	}
定义一个类“吱吱叫” 实现叫的行为接口

	public class Squeak implements QuackBehavior{

	@Override
	public void quack() {
		System.out.println("zhizhi...");
	}
	}

以上定义了接口和实现类，下面是鸭子的抽象类，是鸭子总有些共同点。

	public abstract class Duck {
	public abstract void display();//鸭子的样子
	public void swim(){
		System.out.println("i'm swimming");
	}

	QuackBehavior quackBehavior;
	FlyBehavior flyBehavior;

	public void performFly(){
		flyBehavior.fly();
	}
	public void performQuack(){
		quackBehavior.quack();
	}
	}

现在就可以创建两个鸭子实例了。

	public class ToyDuck extends Duck {
	public DecoyDuck() {
		flyBehavior = new FlyNoway();
		quackBehavior = new Squeak();
		//接口引用变量在父类中已经创建，可以直接拿来用。
		//这里是多态，可以看到多态和接口组合的灵活性。
	}

	@Override
	public void display() {
		System.out.println("my body is yellow");
	}
	public void performFly(){
		flyBehavior.fly();
	}
	public void performQuack(){
		quackBehavior.quack();
	}
	}
另一只鸭子

	public class RedHeadDuck extends Duck{
	
	public RedHeadDuck() {
		quackBehavior = new Quack();
		flyBehavior = new FlyWithWings();
	}

	@Override
	public void display() {
		System.out.println("my head is read");
	}
	public void performFly(){
		flyBehavior.fly();
	}
	public void performQuack(){
		quackBehavior.quack();
	}
	}

下面是测试类

	public class Test {
	public static void main(String[] args) {
		Duck toyDuck = new DecoyDuck();
		toyDuck.performFly();
		toyDuck.performQuack();
		Duck redHeadDuck = new RedHeadDuck();
		redHeadDuck.performFly();
		redHeadDuck.performQuack();
	}
	}
结果：    
I can't fly,i am fake    
zhizhi...    
i can fly     
guagua


### 动态设置行为


在Duck类里新增方法setFlyBehavior()方法可以动态设置飞的行为。

	public void setFlyBehavior(FlyBehavior fb){
		flyBehavior = fb;
	}
新增一个飞行类，用来测试setFlyBehavior（）。

	public class FlyRocketPowered implements FlyBehavior{

	@Override
	public void fly() {
		System.out.println("i'm fly with rocket");
	}
	}
新增一个鸭子类，用来测试

	public class ModelDuck extends Duck{

	public ModelDuck() {
		flyBehavior = new FlyNoway();
		quackBehavior = new Quack();
	}
	@Override
	public void display() {
		System.out.println("i'm modelDuck");
	}
	}
然后是测试

	public class Test {
	public static void main(String[] args) {
		Duck modelDuck = new ModelDuck();
		modelDuck.setFlyBehavior(new FlyRocketPowered());
		modelDuck.performQuack();
		modelDuck.performFly();
	}
	}

guagua     
i'm fly with rocket


设计原则：

- 找出应用中可能需要变化之处，把它们独立出来，不要和那些不需要变化的代码混在一起。
- 针对接口编程，而不是针对实现编程。
- 多用组合，少用继承。

针对接口写更多的实现类，效率更高。而针对实现编程，每次实现都要写一堆代码。

以前的做法是，行为来自Duck的超类的具体实现，要不就重写，或是继承某个接口并由子类自行实现，这两种都是依赖于“实现”，这样就被实现绑的死死的，没办法更改行为（除非写更多代码）。在上面的设计中，鸭子的子类将使用接口所表示的行为，所以实际实现不会被绑死在鸭子的子类中。（也就是，特定的具体行为编写在实现了接口的类中。）