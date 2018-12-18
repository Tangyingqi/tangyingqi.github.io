---
layout: post
title: 'HeadFirst设计模式笔记-命令模式'
tags: [code]
---

### 了解命令模式

定义：

命令模式将请求封装成对象，以便使用不同的的请求，队列或日志来参数化其他对象。命令模式也支持可撤销操作。

命令对象通过在特定接收者上绑定一组动作来封装请求。命令对象将动作个接受者包进对象中，这个对象只对外暴露一个execute（）方法，接受者就会进行动作。从外面看，其他对象不知道究竟哪个接收者进行了哪些动作，只知道如果调用execute()方法，请求的目的就达到了。

![a](http://i5.tietuku.com/cf909c4600d60ad1.png)

- Client： 这个客户负责创建 ConcreteCommand，并设置其接收者。

- Inoker： 这个调用者持有一个命令对象，并在某个时间点调用命令对象的execute()方法，使请求付诸实践。

	这个相当于下面例子的SimpleRemoteControl。

- Command： 所有命令的接口，实现此接口就可传入Inoker。
- ConcreteCommand：这个是命令的具体实现，里面有接受者的引用，目的是调用接受者的方法。

	相当于下面例子的LightOnCommand，接收者是Light。

- Recerver： 接收者


### 举个例子

这个例子是一个遥控，它可以控制灯的开关，在这里只写了开的动作。

	public class Light {
	
	public void on(){
		System.out.println("light is on");
	}
	public void off(){
		System.out.println("light is off");
	}
	}

所有命令都应该实现同一个接口。

	public interface Command {
	public void execute();
	}

下面这个类实现了Command接口，是开灯的命令。

	public class LightOnCommand implements Command{

	Light light;
	public LightOnCommand(Light light) {
		this.light = light;
	}
	@Override
	public void execute() {
		light.on();
	}
	}

这个类里面有设置命令的方法，只要实现了Command接口的命令都可以传入，这样实现了解耦，它不需要知道具体的命令是什么，而且可以随时修改命令。

	public class SimpleRemoteControl {
	Command slot;
	public SimpleRemoteControl(){}
	public void setCommand(Command command){
		slot = command;
	}
	public void buttonWasPressed(){
		slot.execute();
	}
	}

最后是测试类

	public class RemoteControlTest {
	public static void main(String[] args) {
		SimpleRemoteControl remote = new SimpleRemoteControl();
		Light light = new Light();
		LightOnCommand lightOn = new LightOnCommand(light);
		
		remote.setCommand(lightOn);
		remote.buttonWasPressed();
	}
	}

结果：      
	
	light is on

### 其他用途

1.队列请求

将命令打包，形成队列，另一边从队列中取出一个命令，调用execute()方法。工作队列类和进行计算的对象之间是完全解耦的。此刻线程可能在执行财务运算，下一刻却在读取网络数据，工作队列不在乎做什么，它只是取出命令，执行。只要实现命令模式对象，就可以放入队列执行。


### 总结

命令模式将请求封装成对象，这可以让你使用不同的请求，队列来参数化对象。命令模式也可以支持撤销操作。