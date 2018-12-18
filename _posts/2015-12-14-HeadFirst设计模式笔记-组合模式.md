---
layout: post
title: 'HeadFirst设计模式笔记-组合模式'
tags: [code]
---

### 什么是组合模式

定义：组合模式允许你将对象组合成树形结构来表现“整体/部分”层次结构。组合能让客户以一致的方式处理个别对象以及个别对象组合。

简单的说，组合模式就是一个树形结构，根节点就是一个通用的接口，这个接口可以定义一些通用方法。下面是子节点，子节点还可以有自己的子节点。所有节点都实现同一接口。就像一个文件夹里面有文件也有其他文件夹。

![树](http://i4.tietuku.com/65c621d1920514d6.png)

![类图](http://i4.tietuku.com/f5e1a2e80880b832.png)

- 主要解决：它在我们树形结构问题中，模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样处理复杂元素，从而使客户程序与复杂程序内部结构解耦。
- 关键代码：树枝内部组合该接口，并且含有内部属性List，里面放Component
- 优点：1.高层模块调用简单。2.节点自由增加。
- 缺点：在使用组合模式时，其叶子和树枝都是实现类，而不是接口，违反了依赖倒置原则。


### 举个例子

员工下面有领导和普通员工，领导有上层领导，也有下层员工。

首先是一个公用接口

	public interface Employee {
		public void WhoIam();
	}

下面是Worker类

	public class Worker implements Employee{

		private String name;
		public Worker(String name){
			this.name = name;
		}
		@Override
		public void WhoIam() {
			System.out.println("My name is "+getName()+" i am a worker");
		}
		public String getName() {
			return name;
		}
	}

下面是Leader类

	public class Leader implements Employee{

		String name;
		List<Employee> workers = new ArrayList<Employee>();
		public Leader(String name){
			this.name = name;
		}
		public void add(Employee employee){
			workers.add(employee);
		}
		public void remove(Employee employee){
			workers.remove(employee);
		}
		public Employee getChild(int i){
			return workers.get(i);
		}
		@Override
		public void WhoIam() {
			System.out.println("My name is "+getName()+" i am a leader,i have "+workers.size()+" employee");
			for(Employee employee:workers){
				employee.WhoIam();
			}
		}
		public String getName() {
			return name;
		}

	}

测试类

	public class Client {
		public static void main(String[] args) {
			Leader leader1 = new Leader("张三");
			Leader leader2 = new Leader("李四 ");
			Worker worker1 = new Worker("小明");
			Worker worker2 = new Worker("小李");
			Worker worker3 = new Worker("小王");
			Worker worker4 = new Worker("小张");
			leader1.add(leader2);
			
			leader1.add(worker1);
			leader1.add(worker2);
			leader2.add(worker3);
			leader2.add(worker4);
			leader1.WhoIam();
			
		}
	}

结果：
	
	My name is 张三 i am a leader,i have 3 employee
	My name is 李四  i am a leader,i have 2 employee
	My name is 小王 i am a worker
	My name is 小张 i am a worker
	My name is 小明 i am a worker
	My name is 小李 i am a worker