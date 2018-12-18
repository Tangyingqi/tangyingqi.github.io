---
layout: post
title: 'HeadFirst设计模式笔记-观察者模式'
tags: [code]
---


### 观察者模式是什么

HeadFirst设计模式中的定义是：     
观察者模式定义了对象之间的一对多依赖，这样一来，当一个对象改变状态时，它的所有依赖者都会收到通知并自动更新。

举个例子，比如有一家报社，这家报社本身向外提供报纸，但是它只向订阅的人发送报纸。订阅的人就相当于观察者，报社出新报纸，观察者就能看到。当订阅的人取消订阅，报社就不会对其发送报纸。

### 观察者模式要点

- 主题（也就是报社）
- 观察者（也就是订阅的人）

观察者模式主要就是由以上两个元素构成的，这两个元素分别实现两个接口。

- Subject    

	这个接口定义被观察者必须实现的职责，它必须能动态的增加删除观察者，还有提醒观察者信息发生改变。
- Observer    

	这个接口定义观察者接受消息后，进行update操作，对接受的消息进行处理。
- ConcreteSubject
	
	这是被观察者的具体实现类，实现 Subject 接口中定义的方法，定义被观察者自己的业务逻辑，同时定义对哪些事件进行通知。
- ConcreteObserver

	这是观察者的实现类，实现 Observer 接口中的方法，处理自己的业务逻辑。

### 举例说明

下面的例子是天气预报，有一个提供天气数据的，是被观察者。监测天气数据并显示在天气预报上的是观察者。    
首先定义出两个接口：
   
提供天气数据的接口
	
	public interface Subject {
		public void registerObserver(Observer o);
		public void removeObserver(Observer o);
		public void notifyObservers();
	}
观察者接口

	public interface Observer {
		public void update(float temperature,float humidity,float pressure);
	}
显示接口，供观察者实现

	public interface DisplayElements {
		public void display();
	}

实现Subject接口的具体类，相当于提供提供数据的被观察者。

	public class WeatherData implements Subject{

	private ArrayList observers;
	private float temperature;
	private float humidity;
	private float pressure;
	
	public WeatherData() {
		observers = new ArrayList();
	}
	@Override
	public void notifyObservers() {
		for(int i=0;i<observers.size();i++){
			Observer observer = (Observer) observers.get(i);
			observer.update(temperature,humidity,pressure);
		}
	}
	public void measurementsChanged(){
		notifyObservers();
	}
	public void setMeasurements(float temperature,float humidity,float pressure){
		this.temperature = temperature;
		this.humidity = humidity;
		this.pressure = pressure;
		measurementsChanged();
	}

	@Override
	public void registerObserver(Observer o) {
		observers.add(o);
	}

	@Override
	public void removeObserver(Observer o) {
		int i = observers.indexOf(o);
		if(i>0){
			observers.remove(i);
		}
	}
	}

 注意 registerObserver(Observer o)和removeObserver(Observer o)这两个方法，它们的参数都是 Observer 接口，这个在[关于Java回调机制及接口的作用](http://tangyingqi.com/2015/11/05/%E5%85%B3%E4%BA%8EJava%E5%9B%9E%E8%B0%83%E6%9C%BA%E5%88%B6%E5%8F%8A%E6%8E%A5%E5%8F%A3%E7%9A%84%E4%BD%9C%E7%94%A8/)这篇文章里面提到过，使用了多态，只要实现了Observer接口的类，都可以传入，然后被注册。这样增强的可扩展性。在上面这段代码中，传入的观察者被添加到ArrayList里，然后在notifyObservers()方法里面调用update()方法。这样，观察者和被观察者之间通过Observer接口就被连接起来了。

实现Observer接口的具体实现类，也就是观察者。

	public class CurrentConditionsDisplay implements Observer,DisplayElements{

	private float temperature;
	private float humidity;
	private Subject weatherData;
	
	public CurrentConditionsDisplay(Subject weatherData){
		this.weatherData = weatherData;
		weatherData.registerObserver(this);
	}
	@Override
	public void update(float temperature, float humidity, float pressure) {

		this.temperature = temperature;
		this.humidity = humidity;
		display();
	}

	@Override
	public void display() {
		System.out.println("Current Conditions:"+temperature+"F degrees and"
							+humidity+"% humidity");
	}
	}
上面 WeatherData 被观察者调用update()在这个类实现。

下面是测试类

	public class WeatherStation {
	public static void main(String[] args) {
		WeatherData weatherData = new WeatherData();
		CurrentConditionsDisplay currentDisplay = new CurrentConditionsDisplay(weatherData);
		weatherData.setMeasurements(80, 60, 30.4f);
	}
	} 

结果为：    
Current Conditions:80.0F degrees and60.0% humidity

### Java内部提供的观察者模式
在Java内部提供了一个类个一个接口来实现观察者模式

- Observable 类
- Observer 接口

有两种方式，一种是“拉”，也就是被观察者提供get方法，并且调用 notifyObservers() 这个没有参数的方法。一种是“推”，调用 notifyObservers(Object arg)方法。其实变化也不是很大，只改动了一些部分。看下面代码：

	public class WeatherData extends Observable{//继承系统自带的类

	private float temperature;
	private float humidity;
	private float pressure;
	WeatherData weatherData;
	
	public WeatherData() {
	}
	
	public void measurementsChanged(){
		setChanged();//调用此方法指示状态已改变
		notifyObservers();
	}
	public void setMeasurements(float temperature,float humidity,float pressure){
		this.temperature = temperature;
		this.humidity = humidity;
		this.pressure = pressure;
		measurementsChanged();
	}
	//“拉”的方式，提供get方法
	public float getHumidity() {
		return humidity;
	}
	public float getPressure() {
		return pressure;
	}
	public float getTemperature() {
		return temperature;
	
	}
	}
观察者类

	public class CurrentConditionsDisplay implements Observer,DisplayElements{

	Observable observable;//被观察者的引用，用来注册
	private float temperature;
	private float humidity;
	
	public CurrentConditionsDisplay(Observable observable){
		this.observable = observable;
		observable.addObserver(this);
	}

	@Override
	public void display() {
		System.out.println("Current Conditions:"+temperature+"F degrees and"
							+humidity+"% humidity");
	}

	@Override
	public void update(Observable obs, Object arg) {
		if(obs instanceof WeatherData){
			WeatherData weatherData = (WeatherData) obs;
			this.temperature = weatherData.getTemperature();
			this.humidity = weatherData.getHumidity();
			display();
		}
	}
	}

### 总结


自定义的观察者模式，让主题和观察者之间松耦合，它们可以彼此交互，但是不清楚彼此的细节，有新的观察者只需要实现观察者接口，然后注册就能够接受到消息。其实说白了就是定义两个接口，观察者和被观察者实现各自的接口，然后通过观察者接口连接起来。

Java内部提供的方法，省去自己写注册和删除了，提供的“推”和“拉”两种方法。

两种方法各自的缺点：

第一种自己写的方法虽然松耦合，但是它会推送全部信息，无论你想不想要，而且如果增加一条数据，比如体感温度，就要修改每位观察者的调用，而第二种方法只需要修改自身，增加get方法就行了。

使用系统的java.util.Observable(第二种方法)的坏处是，Observable是一个类而不是接口，你必须写一个类继承它，如果想继承其他类就不行了。灵活性太差。

