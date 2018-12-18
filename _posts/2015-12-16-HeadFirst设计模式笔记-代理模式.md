---
layout: post
title: 'HeadFirst设计模式笔记-代理模式'
tags: [code]
---

### 什么是代理模式

定义：代理模式为另一个对象提供一个替身或占位符以控制对这个对象的访问。

简单的说，代理类相当于一个中介，要接触被代理类，需要通过代理类。代理类里面会有被代理类的引用，通过引用调用被代理类中的方法。在代理类里可以加一些方法进行控制。可以想想装饰者模式来理解，它们都是为一个类包装上一些东西。下面会说两者的不同。

![代理模式](http://i4.tietuku.com/f2115338723153fd.png)

- Subject：抽象主题类，它可以是接口也可以是抽象类，被代理类和代理类都要实现此接口。
- RealSubject：具体主题类，也是被代理类，是业务逻辑的具体执行者。
- ProxySubject：代理主题类，它里面有被代理类的引用，以完成具体主题的业务逻辑。



### 为什么使用代理模式

有些对象由于某些原因(比如对象创建开销大，或者某些操作需要安全控制，或者需要进程外的额外访问)，直接访问给系统结构带来很多麻烦。我们会在访问此对象的时候加上一个对此对象的访问层。

例如，图片加载这种耗时操作可以放在代理类里，或者，有些用户权限不允许修改删除，可以在代理类里加以控制。

实际中的代理模式：Windows里面的快捷方式，买东西会去店里而不是工厂。

### 举个例子

这个例子是关于图片处理

首先定义一个统一接口

	public interface Image {
		public void display();
	}

具体主题类

	public class RealImage implements Image{

		private String filename;
		
		public RealImage(String filename) {
			this.filename = filename;
			loadFromDisk(filename);
		}
		
		private void loadFromDisk(String filename) {
			System.out.println("Loading " + filename);
		}
	
		@Override
		public void display() {
			System.out.println("display "+ filename);
		}
	}
代理类

	public class ProxyImage implements Image{

		private String filename;
		RealImage realImage;
		public ProxyImage(String filename) {
			this.filename = filename;
		}
		@Override
		public void display() {
			if(realImage==null){
				realImage = new RealImage(filename);
			}
			realImage.display();
		}
	}

测试类

	public class Test {
		public static void main(String[] args) {
			Image image = new ProxyImage("test.jpg");
			image.display();
		}
	}

结果：

	Loading test.jpg
	display test.jpg

### 总结

代理模式是使用中间人做一些操作，它挡在对象的前面，并负责把请求转发给它们。它跟装饰者模式的不同之处是意图不同，装饰者是给对象添加装饰，也就是增加行为，而代理模式是给对象增加控制。