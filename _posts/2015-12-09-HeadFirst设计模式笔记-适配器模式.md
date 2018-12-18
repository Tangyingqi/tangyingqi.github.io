---
layout: post
title: 'HeadFirst设计模式笔记-适配器模式'
tags: [code]
---

### 什么是适配器

定义：将一个类的接口，转换成客户期望的另一个接口。适配器让原本接口不兼容的类可以合作无间。

举个现实中的例子，你去一个国家旅游，这个国家的充电插头和你充电器上的插头是不匹配的，通常需要一个转换插头。这个转换插头就是适配器。它的作用是把两个不能连接起来工作的东西连接起来。

### 举个例子

中国旅客住在德国一家宾馆，德国的插头是两项插头，中国的是三项插头。这个时候就需要适配器。

	public interface GermanSocketInterface {
	public void charging();
	}

	public class GermanSocket implements GermanSocketInterface{

	@Override
	public void charging() {
		System.out.println("使用德国两项插头充电");
	}

	}

下面是中国的充电插头

	public interface ChinaSocketInterface {
	public void charging();
	}

	public class ChinaSocket implements ChinaSocketInterface{

	@Override
	public void charging() {
		System.out.println("使用中国三项插头充电");
	}
	}

下面是德国的宾馆

	public class Hotel {
	private GermanSocketInterface Gsocket;
	public Hotel(){}
	public Hotel(GermanSocketInterface Gsocket){
		this.Gsocket = Gsocket;
	}
	public void setSocket(GermanSocketInterface Gsocket){
		this.Gsocket = Gsocket;
	}
	public void charge(){
		Gsocket.charging();
	}
	}

在德国的宾馆里是德国式的插孔，它只接受德式接口，也就是我们程序里面实现德式接口的类。

下面是适配器。

	public class ChargeAdapter implements GermanSocketInterface{

	private ChinaSocket Csocket; 
	public ChargeAdapter(ChinaSocket Csocket) {
		this.Csocket = Csocket;
	}
	@Override
	public void charging() {
		System.out.println("插上转换器");
		Csocket.charging();
	}

	}

这个适配器实现了德式接口，可以传入上面的setSocket方法，然后它还有中式插头的引用，可以实现插上中式插头。也就是，它前面是两项插头，然后屁股保有三项插头的孔。这就是转换器。

![转换插头](http://i5.tietuku.com/8fb421a6166bf6ca.jpg)

测试类

	public class Test {
	public static void main(String[] args) {
		
		Hotel hotel = new Hotel();
		ChinaSocket Csocket = new ChinaSocket();
		ChargeAdapter adpter = new ChargeAdapter(Csocket);
		hotel.setSocket(adpter);
		
		hotel.charge();
	}
	}
结果：

	插上转换器
	使用中国三项插头充电

在测试类里面看起来，适配器模式和装饰者模式有点像，其实他们的意图是完全不同的。装饰者是为了给被装饰者添加新的行为。而适配器是为了转换接口，让两个类能连接起来。