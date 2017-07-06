---
title: 为什么extends是魔鬼
date: 2017-01-06 16:02:08
tags:
  - Java
  - 继承
  - 翻译
categories: Java

---
# 为什么extends是魔鬼
**_通过使用接口替换具体的base类来改进你的代码_**

[原文地址](http://www.javaworld.com/javaworld/jw-08-2003/jw-0801-toolbox.html)

# 前言
　　`extends`关键字是魔鬼。可能还没有达到Charles Manson的程度，但是也已经达到了需要敬而远之的地步。四人帮的设计模式一书详尽的讨论了使用接口继承(`implements`)替代实现继承(`extends`)。

　　好的设计者基于接口编写代码而不是依靠具体的基础类。这篇文章阐述了为什么设计者有这种古怪习惯，以及介绍一些基于接口的编程基础。
　　
## 接口与类

　　一次我去参加一个Java用户小组会议，当时Java的发明者James Gosling是主讲人。在那个另人难忘的提问环节，有人问他：“如果给你个机会重新设计JAVA，你会做些什么改变？”。他回答道：“我会去除类”。在哄堂大笑之后他解释到从本质上问题不出在类本身而出在实现继承关系上。(`extends`关系)。接口继承（`implements`关系）是更好的。你应该尽可能的避免实现继承关系无论在何时。
<!--more-->
## 失去灵活性
　　为什么要避免实现继承关系？首当其冲的问题是明确使用具体的类名会把你与特定的实现捆绑在一起，这给以后的变更带来了不必要的麻烦。

　　设计和开发并行是现代敏捷开发理念的核心观点。在完全设计好程序之前就已经开始着手开发了。这种技术悍然无视传统理论中开发之前必须有完备设计的观念，但是很多的成功项目都证明比起传统途径来你可以使用敏捷理念来更快速更有性价比的开发高质量代码。然而，在并行开发的理念中灵活性是核心理念。你不得不使你的代码能够做到只需很小的改动就能把新需求融合到现有的代码中去。

　　只实现明确需要的特性而不是实现可能需要的特性并且包容变化。没有这种灵活性，并行开发是不可能的。

　　面向接口编程是灵活结构的核心。为了说清为什么，让我们来看看当不用的时候会发生什么。思考以下代码

	f()
	{   LinkedList list = new LinkedList();
		//...
		g( list );
	}
	g( LinkedList list )
	{
		list.add( ... );
		g2( list )
	}
　　   现在设想一个新的快速查找的需求出现了，因此LinkedList不能解决问题。你需要使用HashSet取代。在现在的代码里，这个改动不局限于一处因为你必须同事改动f函数和g函数以及所有像g函数那样获取这个list的方法。
　　重写代码如下:

	f()
	{   Collection list = new LinkedList();
		//...
		g( list );
	}
	g( Collection list )
	{
		list.add( ... );
		g2( list )
	}
　　重构使得只需要简单的把new LinkedList( )替换成new HashSet( ) 酒可以把Link list改成hash表。就是这么神奇，不要其他任何改动。

　　另一个例子，比较下以下两段代码：
	
	f()
	{   Collection c = new HashSet();
		//...
		g( c );
	}
	g( Collection c )
	{
		for( Iterator i = c.iterator(); i.hasNext() ;)
		do_something_with( i.next() );
	}
	f2()
	{   Collection c = new HashSet();
		//...
		g2( c.iterator() );
	}
	g2( Iterator i )
	{   while( i.hasNext() ;)
		do_something_with( i.next() );
	}
　　g2函数现在不仅可以遍历Collection的衍生对象也可以遍历像Map这种键值列表。事实上，你能使用迭代器生成数据而不是遍历集合。你可以使用迭代器为程序从测试框架或者文件中收集信息。这里面有巨大的灵活性

## 耦合
　　实现继承的一个更关键的问题时耦合——一部分模块对另一部分不恰当的依赖。强耦合带来麻烦最经典的例子是全局变量。比如你改变了全局变量的类型，所有使用这个变量（也就是耦合于此变量）的所有函数都会收到影响，因此所有的代码都必须被检查、修改、重新测试。进一步，所有函数都因为使用了这个变量而被彼此耦合在一起。如果一个方法在不恰当的时间修改了变量的值那么它就可能错误的影响了其他函数的表现。这个问题在多线程的程序中特别的可怕。

　　作为一个设计这，你应该致力于构建最小的耦合关系。你不能完全消除耦合因为一个类对象调用另一个对象的方法是松耦合的一种形式。一个没有耦合的程序是天方夜谭。虽然如此，你只要闭着眼睛去遵循OO的戒律（最重要的是一个类的实现应该对调用的类完全隐藏）就能把耦合降到最低。比如类实例变量（不是常量的成员字段）应该始终是私有的。句号！毫无例外！永远！（你能偶尔有效地使用`protected`方法，但是`protected`实例变量是被唾弃的）你永远不应该使用get/set方法因为相同的原因——它只是用一个非常复杂的方法使成员变成了共有的（虽然返回成熟对象的存取方法而不是基本类型值在某些情况下是合理的，比如当返回的对象在设计中是一个关键的抽象）

　　我不是在这卖弄学问。我已经找到了一条途径去解决严格的OO方法、快速开发和易维护性之间的矛盾。每当我违反一条OO的核心原则类似实现隐藏，最终否导致我必须重写那些代码（最常见的原因是不能进行debug）。我没有时间重写代码，所以我遵循这些规则。我的顾虑都是很实际的而不是为了完美而完美。

## 脆弱的基础类问题
　　现在，让我们把耦合的概念应用到继承.在一个使用`extends`的实现继承系统里,衍生类与基础类有着非常紧密的耦合。这种密切的联系是不可取的。设计者给这种行为起了个绰号“脆弱的基础类问题”。基础类被认为是脆弱的因为你能用一种看似看全的方法修改技术类，但是这个新的行为当被衍生类继承后可能导致衍生类出现故障。你不能简单说一个基础类是否安全仅仅是通过单独测试基础类的方法。你也必须测试所有的衍生类。进一步，你必须检查所有使用基础类和衍生类对象的代码，因为这些代码可能被新的行为破坏。一个简单的关键基础类的变动可能导致整个程序无法工作。

　　让我们一起检查脆弱的基础类和基础类耦合问题。下面的类继承了Java的ArrayList类并添加一个类似于栈的新行为：

	class Stack extends ArrayList
	{   private int stack_pointer = 0;
		public void push( Object article )
		{   add( stack_pointer++, article );
		}
		public Object pop()
		{   return remove( --stack_pointer );
		}
		public void push_many( Object[] articles )
		{   for( int i = 0; i < articles.length; ++i )
			push( articles[i] );
		}
	}
　　如此之简单的类都会有问题。考虑当一个用户利用继承并使用`ArrayList`的`clear()`方法去清空栈：

	Stack a_stack = new Stack();
	a_stack.push("1");
	a_stack.push("2");
	a_stack.clear();
　　代码成功编译，但因为基础类不知道`stack_pointer`的存在，这个栈对象现在是没有定义的状态。下一个调用`push()`你压入新的元素在序列2处（`stack_pointer`当前值），因此栈事实上有3个元素，底部的两个变成了脏数据。（Java的栈类有相同的问题，不要使用他）。

　　一个解决这个方法继承不当的问题是为栈重写所有的`ArrayList`方法使其能修改队列状态，重写那些方法去正确操作`stack_pointer`或者抛出异常。（`removeRange()`方法是一个很好的抛出异常的候选者）

　　这个方法有两个劣势。首先，你重写了所有方法，基础类实际上变成了一个接口，不是一个类。既然你不使用任何继承方法那么实现继承还有任何意义吗？其次，一个更重要的原因，你不想栈支持所有的`ArrayList`方法。例如烦人的`removeRange`方法就毫无用处。对于继承来的无用的方法唯一可行的办法是抛出异常，因为它应该永远不会被调用。这种方式实际上把一个编译错误变成了运行时错误。非常不好。如果一个方法不是简单因为未被声明而被编译器检查出方法未找到错误。如果方法仅仅是抛出异常，那么你在程序运行钱都不会发现调用错误。

　　一个更好的基础类解决方案是封装数据机构代替使用继承。下面是一个新的改进过的栈版本

	class Stack
	{   private int stack_pointer = 0;
		private ArrayList the_data = new ArrayList();
		public void push( Object article )
		{   the_data.add( stack_pointer++, article );
		}
		public Object pop()
		{   return the_data.remove( --stack_pointer );
		}
		public void push_many( Object[] articles )
		{   for( int i = 0; i < o.length; ++i )
			push( articles[i] );
		}
	}
　　到目前为止看起来还不错，但是考虑考虑脆弱的基础类问题。让我们看下当你想创建一个栈的变体用来跟踪确定时间端内栈的最大值，一个可能的实现如下所示：

	class Monitorable_stack extends Stack
	{
		private int high_water_mark = 0;
		private int current_size;
		public void push( Object article )
		{   if( ++current_size > high_water_mark )
			high_water_mark = current_size;
			super.push(article);
		}
		public Object pop()
		{   --current_size;
			return super.pop();
		}
		public int maximum_size_so_far()
		{   return high_water_mark;
		}
	}
　　这个新对象运行正常，至少暂时是这样，不幸的是这段代码利用了一个事实那就是`push_many()`是通过调用`push()`来完成它的工作的。其实，这些细节似乎不是一个坏的选择。这简化了代码，你通过重载`push()`版本来实现，即使当`Monitorable_stact`通过获得一个`stack`引用来实现的，所以`high_water_mark`正确的更新了。
　　
　　在一个阳光明媚的日子，某人可能跑了一个性能测试并且注意到Stack类在被大量使用时运行效率不太理想。你决定重写Stack并且放弃使用ArrayList，因此你提高了Stack类的性能。下面是新类的部分核心代码：

	class Stack
	{   private int stack_pointer = -1;
		private Object[] stack = new Object[1000];
		public void push( Object article )
		{   assert stack_pointer < stack.length;
			stack[ ++stack_pointer ] = article;
		}
		public Object pop()
		{   assert stack_pointer >= 0;
			return stack[ stack_pointer-- ];
		}
		public void push_many( Object[] articles )
		{   assert (stack_pointer + articles.length) < stack.length;
			System.arraycopy(articles, 0, stack, stack_pointer+1,
								articles.length);
			stack_pointer += articles.length;
		}
	}
　　注意`push_many()`不再重复调用`push( )`而是使用了块转移。新的栈版本工作的很好；事实上，它比前个版本要更好。不幸的是，`Monitorable_stack`衍生类将不再工作，因为当`push_many`被调用是它不能正确的跟踪栈的使用情况。（衍生类的push版本不再被`push_many`调用，所以`push_many`也不再更新high_water_mark）。Stack就是脆弱的基础类。事实证明，通过谨慎的做法来解决这些问题几乎是不可能的。
　　
　　记住，如果你使用接口继承就不会有这些问题，因为没有继承的方法会对你产生不利的影响。如果Stack是接口，通过同时实现`Simple_stack`和`Monitorable_stack`来使用,代码会变得更健壮。
　　
　　我在清单0.1中提供一个基于接口的解决方案。这个解决方案和实现继承方案有相同的灵活性：你能在Stack抽象基础上构建你的代码为不用担心你究竟是在操作哪种具体类型的栈。既然两个实现类必须提供公共接口中的所有东西的实现版本，因此很难出现前面所讲的问题。虽然不使用基础类我仍然能获得类似的好处，因为我使用了封装而不是派生。另一方面，在封装类中我不得不使用没有价值的访问方法去访问默认的实现。(比如在第41行`Monitorable_Stack.push(…）`不得不在`Simp_stack`中调用`equivalent`方法。程序员总是抱怨写这些看似多余的代码，但是多些一行代码的代价比起一个消除一个严重的隐藏bug来还是微不足道的。
Listing 0.1. Eliminate fragile base classes using interfaces

	 1| import java.util.*;
	 2| 
	 3| interface Stack
	 4| {
	 5|　　 	void push( Object o );
	 6|　　 	Object pop();
	 7|　　 	void push_many( Object[] source );
	 8| }
	 9| 
	10| class Simple_stack `implements` Stack
	11| {   private int stack_pointer = -1;
	12|　　 	private Object[] stack = new Object[1000];
	13| 
	14|　　 public void push( Object o )
	15|　　 {   	assert stack_pointer < stack.length;
	16| 
	17|　　 		stack[ ++stack_pointer ] = o;
	18|　　 }
	19| 
	20|　　 public Object pop()
	21|　　 {   	assert stack_pointer >= 0;
	22| 
	23|　　 		return stack[ stack_pointer-- ];
	24|　　 }
	25| 
	26|　　 public void push_many( Object[] source )
	27|　　 {   	assert (stack_pointer + source.length) < stack.length;
	28| 
	29|　　 		System.arraycopy(source,0,stack,stack_pointer+1,source.length);
	30|　　 		stack_pointer += source.length;
	31|　　 }
	32| }
	33| 
	34| 
	35| class Monitorable_Stack `implements` Stack
	36| {
	37|　　 private int high_water_mark = 0;
	38|　　 private int current_size;
	39|　　 Simple_stack stack = new Simple_stack();
	40| 
	41|　　 public void push( Object o )
	42|　　 {   if( ++current_size > high_water_mark )
	43|　　　　 		high_water_mark = current_size;
	44|　　 		stack.push(o);
	45|　　 }
	46|　　 
	47|　　 public Object pop()
	48|　　 {   	--current_size;
	49|　　 		return stack.pop();
	50|　　 }
	51| 
	52|　　 public void push_many( Object[] source )
	53|　　 {
	54|　　 		if( current_size + source.length > high_water_mark )
	55|　　　　		high_water_mark = current_size + source.length;
	56| 
	57|　　 		stack.push_many( source );
	58|　　 }
	59| 
	60|　　 public int maximum_size()
	61|　　 {   return high_water_mark;
	62|　　 }
	63| }
	64| 
## 框架
　　脱离了具体的程序架构来谈脆弱的基础类问题就是耍流氓。像MFC这样的框架已经成为了类库构建的流行方式。尽管MFC本身正在令人欣喜的逐渐衰弱，但是在微软商店里MFC框架还是拥有统治地位因为那里的程序员相信微软即正义。
　　基于框架的系统通常开始于库提供的半成品类，你不需要做任何事，只要通过派生类去提供缺失的方法。典型的例子是Java的`Component`类里的`paint`方法，这个方法只不过是一个占位符；派生类必须提供真实的实现版本。
　　你能适当的避免这类事情，但是基于类派生订制实现的类框架是极其脆弱的。基础类太脆弱了。当我在MFC中开发程序，我就要有随时改写程序以应对微软发布新版本的觉悟。今天可用的代码到了明天就未必了，因为基础类方法变了。
　　所有的java包都是即插即用工作良好。你不需要继承任何东西并实现方法。这种即插即用的工作机构要好于基于派生的框架。它更易维护和使用并且不会把你的代码置于因为Sun公司修改类库实现而崩溃的风险之下。

## 总结脆弱的基础类
　　通常来讲，最好是避免使用具体的基础类和`extends`继承关系而以接口和`implements`实现关系取代。我的经验法则是我最少80%的代码需要基于接口重构。比如，我永远不会使用HashMap的引用，而是使用Map接口。（我这里使用宽泛的“接口”定义。当你看到InputStream如何实现你就会知道实际上它也是个接口，尽管在Java中是需要被实现的抽象类。）
　　你使用越多的抽象，你就能获得越大的灵活性。在如今的商业环境里，开发过程中需求不断变化，这种灵活性是必须的。进一步，绝大多数的敏捷开发方法论（比如Crystal和极限开发）无法实施除非代码都是基于抽象的。
　　如果你仔细阅读四人帮的设计模式，你会发现他们提供的许多方法都是消除了实现继承取而代之的是接口继承，这是大多数模式的普遍特征。这个重要的事实就是我们开头所说的：设计模式是被发现的而不是被发明的。设计模式总结了那些优美的、易于维护的代码。他告诉我们优美的、易于维护的代码都不惜一切代价避免实现继承。