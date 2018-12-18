---
layout: post
title: 'HeadFirst设计模式笔记-状态模式'
tags: [code]
---


### 什么是状态模式

定义：状态模式允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

简单的说就是把状态封装成单独的类，并将动作委托到代表当前状态的对象

![类图](http://i12.tietuku.com/9e0372ee7183a393.png)

上面的就是状态模式的类图，它跟策略模式很像，可以回想策略模式来理解状态模式。它们都是通过对象的组合实现动态的改变行为。

### 什么时候使用状态模式

不使用状态模式的时候，我们会定义一些静态变量，为这些变量赋值不同的数字，来表示各个状态。这样的方法如果需要增加状态是非常不方便的。状态模式可以使状态之间解耦，可以随时创建新的状态，只要实现共同的接口。

### 举个例子

下面这个例子是关于自动售卖机的，它有如下几个状态

- 没投币
- 已投币
- 售罄
- 投币之后出货状态

这些状态都有统一的接口，实现统一的方法，只是实现各有不同。然后通过setState()方法动态的转换状态。

下面是统一的接口

	public interface State {
		public void insertQuarter();//投币
		public void ejectQuarter();//退币
		public void turnCrank();//摇杆
		public void dispense();//出货
	}	

没币的状态

	public class NoQuarterState implements State{
	
		GumballMachine gumballMachine;
		
		public NoQuarterState(GumballMachine gumballMachine){
			this.gumballMachine = gumballMachine;
		}
	
		@Override
		public void dispense() {
			System.out.println("You need to pay fitst");
		}
	
		@Override
		public void ejectQuarter() {
			System.out.println("You haven't inserted a quarter");
		}
	
		@Override
		public void insertQuarter() {
			System.out.println("You inserted a quarter");
			gumballMachine.setState(gumballMachine.getHasQuarterState());
		}
	
		@Override
		public void turnCrank() {
			System.out.println("You turned,but there's no quarter");
		}
	}

有币的状态

	public class HasQuarterState implements State{

		GumballMachine gumballMachine;
		public HasQuarterState(GumballMachine gumballMachine){
			this.gumballMachine = gumballMachine;
		}
		@Override
		public void dispense() {
			//System.out.println("No gumball dispensed");
			//这个方法在这里没什么用
		}
	
		@Override
		public void ejectQuarter() {
			System.out.println("Quarter returned");
			gumballMachine.setState(gumballMachine.getSoldState());
		}
	
		@Override
		public void insertQuarter() {
			System.out.println("You can't insert another quarter");
		}
	
		@Override
		public void turnCrank() {
			System.out.println("You turned...");
			gumballMachine.setState(gumballMachine.getSoldState());
		}

	}

售罄状态

	public class SoldOutState implements State{

		GumballMachine gumballMachine;
		public SoldOutState(GumballMachine gumballMachine){
			this.gumballMachine = gumballMachine;
		}
		@Override
		public void dispense() {
			System.out.println("Sorry,no gumballs dispense");
		}
	
		@Override
		public void ejectQuarter() {
			System.out.println("Sorry,You can't eject,you haven't inserted a quarter yet");
			
		}
	
		@Override
		public void insertQuarter() {
			System.out.println("Sorry,you can't insert a quarter,the machine is sold out");
		}
	
		@Override
		public void turnCrank() {
			System.out.println("Sorry,there are no gumballs");
		}
	}

出货状态

	public class SoldState implements State{

		GumballMachine gumballMachine;
		public SoldState(GumballMachine gumballMachine){
			this.gumballMachine = gumballMachine;
		}
		@Override
		public void dispense() {
			gumballMachine.releaseBall();
			if(gumballMachine.getCount()>0){
				gumballMachine.setState(gumballMachine.getNoQuarterState());
			}else{
				System.out.println("Oops,out of gumballs");
				gumballMachine.setState(gumballMachine.getSoldOutState());
			}
		}
	
		@Override
		public void ejectQuarter() {
			System.out.println("you already turned the crank");
		}
	
		@Override
		public void insertQuarter() {
			System.out.println("please wait,we're already giving you a gumball");
		}
	
		@Override
		public void turnCrank() {
			System.out.println("Turning twice doesn't get you another gumball");
		}
	}

售卖机的类

	public class GumballMachine {

		State noQuarterState;
		State hasQuarterState;
		State soldState;
		State soldOutState;
		
		State state = soldOutState;
		int count = 0;
	
		public GumballMachine(int numberGumballs) {
			noQuarterState = new NoQuarterState(this);
			hasQuarterState = new HasQuarterState(this);
			soldState = new SoldState(this);
			soldOutState = new SoldOutState(this);
			this.count = numberGumballs;
			if(numberGumballs>0){
				state = noQuarterState;
			}
		}
	
		public void setState(State state) {
			this.state = state;
		}
		public void insertQuarter(){
			state.insertQuarter();
		}
		public void ejectQuarter(){
			state.ejectQuarter();
		}
		public void turnCrank(){
			state.turnCrank();
			state.dispense();
		}
	
		public void releaseBall(){
			System.out.println("A gumball comes rolling out the slot...");
			if(count!=0){
				count = count-1;
			}
		}
	
		public State getNoQuarterState() {
			return noQuarterState;
		}
	
		public State getHasQuarterState() {
			return hasQuarterState;
		}
	
		public State getSoldState() {
			return soldState;
		}
	
		public State getSoldOutState() {
			return soldOutState;
		}
	
		public int getCount() {
			return count;
		}
	}

售卖机类里持有所有状态的引用，内部转换状态通过setState()方法，然后通过被动态设置的state调用相应的方法。

测试类

	public class GumballMachineTest {
		public static void main(String[] args) {
			GumballMachine gumballMachine = new GumballMachine(5);
			gumballMachine.insertQuarter();
			gumballMachine.turnCrank();
			gumballMachine.insertQuarter();
			gumballMachine.ejectQuarter();
			gumballMachine.ejectQuarter();
		}
	}

结果：

	You turned...
	A gumball comes rolling out the slot...
	You inserted a quarter
	Quarter returned
	you already turned the crank

### 总结

通过策略模式理解状态模式就很好理解了，它们基本是一样的，只是意图不同。那么，它们究竟是怎么的不同，怎么区分它们。      
策略模式要做的是定义一些策略，让他的对象去使用。想想鸭子那个例子，它让那些行为组合起来表示的是一个整体，一只完整的鸭子。而鸭子的属性是固定的，被固定好就不会改变。而状态模式定义的就是一些状态，它会随时动态的改变，也就是说，使用状态模式就是要不断的改变行为，这是它的初衷。