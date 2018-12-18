---
layout: post
title: 'HeadFirst设计模式笔记-迭代器模式'
tags: [code]
---


### 什么是迭代器模式

定义：提供一种方法顺序访问一个聚合对象中的各个元素，而又不暴露其内部的表示。

### 为什么要用迭代器

存储方式有很多种，有集合和数组，如果不同的类使用不同的存储方式，这样遍历起来就很麻烦。可能要写几个for 循环做遍历这件事。如果有迭代器的话，就不会有这样的问题，它内部封装了遍历的方法。让任何实现它接口的集合都可以用它内部实现方法去遍历。

Java内部有自己的迭代器 Iterator，也可以自己实现迭代器。

### 举个例子

这个例子是关于菜单，两份不同的菜单，它们有不同的存储方式，一个是数组存储，一个是集合存储。

首先是迭代器接口

	public interface Iterator {
	boolean hasNext();
	Object next();
	}
然后是两个迭代器接口的具体实现，它们分别是两家不同的餐厅

	public class DinerMenuIterator implements Iterator{

	MenuItem[] items;
	int position = 0;
	public DinerMenuIterator(MenuItem[] items){
		this.items = items;
	}
	@Override
	public boolean hasNext() {
		if(position >= items.length || items[position] == null){
			return false;
		}else{
			return true;
		}
	}

	@Override
	public Object next() {
		MenuItem menuItem = items[position];
		position = position + 1;
		return menuItem;
	}
	}

另一家餐厅迭代器
	
	public class PancakeIterator implements Iterator{

	List<MenuItem> menuItems;
	int position = 0;
	
	public PancakeIterator(List<MenuItem> menuItems) {
		this.menuItems = menuItems;
	}
	@Override
	public boolean hasNext() {
		if(position > menuItems.size()-1 || menuItems.get(position)==null){
			return false;
		}else{
			return true;
		}
	}

	@Override
	public Object next() {
		MenuItem menuItem = menuItems.get(position);
		position++;
		return menuItem;
	}
	}
菜单接口

	public interface Menu {
	public Iterator createIterator();
	}

下面是俩家餐厅都同意的一个菜单模板，后面两家都以此创建对象。

	public class MenuItem {
	String name;
	String description;
	boolean vegetarian;
	double price;
	
	public MenuItem(String name,String description,boolean vegetarian,double price){
		this.name = name;
		this.description = description;
		this.vegetarian = vegetarian;
		this.price = price;
	}
	public String getName() {
		return name;
	}
	public String getDescription() {
		return description;
	}
	public double getPrice() {
		return price;
	}
	public boolean isVegetarian() {
		return vegetarian;
	}
	}

第一家菜单

	public class DinerMenu implements Menu{
	static final int MAX_ITEMS = 6;
	int numberOfItems = 0;
	MenuItem[] menuItems;

	public DinerMenu() {
		menuItems = new MenuItem[MAX_ITEMS];
		addItem("Vigetarian BLT", "Bacon with lettuce & tomato on whole wheat",
				true, 2.99);
		addItem("Soup of the day",
				"Soup of the day with a side of potato salad", false, 3.29);
		addItem("BLT", "Bacon with lettuce & tomato on whole wheat", false,
				2.99);
		addItem("Hotdog",
				"a hot dog,with saurkraut,relish,onions,topped with cheese",
				false, 3.05);
	}

	public void addItem(String name, String description, boolean vegetarian,
			double price) {
		MenuItem menuItem = new MenuItem(name, description, vegetarian, price);
		if (numberOfItems >= MAX_ITEMS) {
			System.err.println("Sorry,menu is full!Can't add item to menu");
		} else {
			menuItems[numberOfItems] = menuItem;
			numberOfItems = numberOfItems + 1;
		}

	}
	@Override
	public Iterator createIterator() {
		return new DinerMenuIterator(menuItems);
	}
	}
别看那么多代码，很多都是废话。它就是创建对象，存储到数组中。createIterator()这个方法不懂可以暂时忽略。

第二家菜单

	public class PancakeHouseMenu implements Menu{
	ArrayList<MenuItem> menuItems;

	public PancakeHouseMenu() {
		menuItems = new ArrayList<MenuItem>();

		addItem("K&B's Pancake Breakfast",
				"Pancakes with scrambled eggs, and toast", true, 2.99);
		addItem("Regular Pancake Breakfast",
				"Pancakes with fried eggs, sausasge", false, 2.99);
		addItem("Blueberry Pancakes", "Pancakes made with fresh blueberries",
				true, 3.49);
		addItem("Waffles",
				"Waffles,with your vhoice of blueberries or strawberries",
				true, 3.59);
	}

	public void addItem(String name, String description, boolean vegerarian,
			double price) {
		 MenuItem menuItem = new MenuItem(name,description,vegerarian,price);
		 menuItems.add(menuItem);
	}
	@Override
	public Iterator createIterator() {
		return new PancakeIterator(menuItems);
	}
	}
这家跟上家差不多，只不过它的存储方式为集合。

下面这个类是服务员，它负责点餐，迭代器模式方便了她，只用一种方式就遍历了。

	public class Waitress {
	Menu pancakeHouseMenu;
	Menu dinerMenu;
	
	public Waitress(Menu pancakeHouseMenu,Menu dinerMenu) {
		this.pancakeHouseMenu = pancakeHouseMenu;
		this.dinerMenu = dinerMenu;
	}
	public void printMenu(){
		Iterator dinerMenuIterator = dinerMenu.createIterator();
		System.out.println("MENU\n----\nbreakfase:");
		printMenu(dinerMenuIterator);
		
		
		System.out.println("------------------");
		Iterator pancakeIterator = pancakeHouseMenu.createIterator();
		System.out.println("lunch:");
		printMenu(pancakeIterator);
	}
	private void printMenu(Iterator iterator){
		while(iterator.hasNext()){
			MenuItem menuItem = (MenuItem) iterator.next();
			System.out.print(menuItem.getName()+",");
			System.out.print(menuItem.getPrice()+" ");
			System.out.println(menuItem.getDescription()+"--");
		}
	}
	}
createIterator() 方法在这里起作用了，被调用创建对应的迭代器对象，然后通过这个对象遍历。

测试类

	public class MenuTest {
	public static void main(String[] args) {
		DinerMenu dinerMenu = new DinerMenu();
		PancakeHouseMenu pancakeHouseMenu = new PancakeHouseMenu();
		Waitress waitress = new Waitress(pancakeHouseMenu,dinerMenu);
		waitress.printMenu();
	}
	}
结果：

	MENU
	----
	breakfase:
	Vigetarian BLT,2.99 Bacon with lettuce & tomato on whole wheat--
	Soup of the day,3.29 Soup of the day with a side of potato salad--
	BLT,2.99 Bacon with lettuce & tomato on whole wheat--
	Hotdog,3.05 a hot dog,with saurkraut,relish,onions,topped with cheese--
	------------------
	lunch:
	K&B's Pancake Breakfast,2.99 Pancakes with scrambled eggs, and toast--
	Regular Pancake Breakfast,2.99 Pancakes with fried eggs, sausasge--
	Blueberry Pancakes,3.49 Pancakes made with fresh blueberries--
	Waffles,3.59 Waffles,with your vhoice of blueberries or strawberries--

上面的代码由于废话太多显得有点乱，我们来整理一下。


![1](http://i12.tietuku.com/5f9f52d430ea756d.png)

DinerMenu 和 PancakeHouseMenu 里面都有 createIterator 方法返回对应的菜单迭代器对象。在Waitress 里面调用这个方法得到相应的迭代器进行遍历。为什么迭代器可以遍历菜单的内容？因为迭代器对象的构造方法里面传入了一个菜单对象，所以可以遍历。


### 总结

- 迭代器模式适用于不同的存储方法遍历，迭代器可以封装方法，从而对任何实现迭代器接口的进行遍历。

- 迭代器模式让我们能游走于聚合内的每一个元素，而又不暴露其内部的表示。

- 把游走的任务放在迭代器上，而不是聚合上。这样简化了聚合的接口和实现，也让责任各得其所。

- 迭代器模式符合单一责任，单一责任原则即一个类应该只有一个引起变化的原因。迭代器就是把多个变为一个。