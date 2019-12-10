---

layout: post
title: 函数式编程
category: 架构
tags: Architecture
keywords: functional programming

---

## 简介

* TOC
{:toc}

可预先看下 [java 语言的动态性](http://qiankunli.github.io/2018/08/15/java_dynamic.html)  类型系统 部分 内容

为什么开始热门了？不管是面向过程，还是面向对象，解决的都还是代码组织问题。我们知道，软件生命周期 最大的过程是维护，维护基本就是debug，debug过程中又会产生新的bug。那么换个思路，干脆在编程阶段，如何编写 易维护的代码？换句话说，哪些因素导致代码容易出bug？函数式编程 在某些层面规避了这些。

## 编程范式异同

[声明式编程范式初探](http://www.nowamagic.net/academy/detail/1220525)

![](/public/upload/architecture/programming_paradigm.jpg)

命令式编程是行动导向（Action-Oriented）的，因而算法是显性而目标是隐性的；声明式编程是目标驱动（Goal-Driven）的，因而目标是显性而算法是隐性的。声明式编程是人脑思维方式的抽象，即利用数理逻辑或既定规范对已知条件进行推理或运算。已知 ==> 未知。

命令式编程中的变量本质上是抽象化的内存，变量值是该内存的储存内容。声明式编程让我们重回数学思维：函数式编程类似代数中的表达式变换和计算，逻辑式编程则类似数理逻辑推理。其中的变量也如数学中的一样，是抽象符号而非内存地址，因此没有赋值运算，不会产生变量被改写的副作用（side-effect），也不存在内存分配和释放的问题。

声明式语言与命令式语言的相通之处

1. 首先，所有高级语言都建立于低级语言之上，最终转化为机器语言，声明式语言也不例外。
2. 其次，声明式语言与命令式语言并非泾渭分明，而是互相交叉渗透的。一些‘非纯粹’ 的声明式语言也提供变量赋值和流程控制，而一些命令式语言也在逐渐发展，通过利用其他程序或增加新的语言特征来实现声明式编程。

总的说来，在命令式语言中融入声明式的元素应当是一种趋势。尤其是函数式，它的一些特征已经在许多命令式语言中得到了支持。

既然声明式编程有这么多好处，为什么命令式语言不仅占大多数，而且流行程度也不减呢？编程语言的流行程度与其擅长的领域关系密切。声明式语言——尤其是函数式语言和逻辑式语言——擅长基于数理逻辑的应用，如人工智能、符号处理、数据库、编译器等，对基于业务逻辑的、尤其是交互式或事件驱动型的应用就不那么得心应手了。而大多数软件是面向用户的，交互性强、多为事件驱动、业务逻辑千差万别，显然命令式语言在此更有用武之地。PS：[《数据结构与算法之美》——算法新解](http://qiankunli.github.io/2019/01/21/beauty_of_algorithm_2.html) 这一点就好比 你不搞垃圾过滤不知道贝叶斯，不搞人工智能不知道数学有用一样。

## 《反应式设计模式》

2019.3.24补充

在1995年到2008年的这段时期，可谓是函数式编程的”中世纪“，C++/Java等编程语言的使用率不断增加，而命令式的、面向对象的编程风格成为编写应用程序和解决问题最流行的方式。终于，**多核心cpu的到来为并行化开辟了新机会**， 在这样的环境中，具有副作用的命令式编程结构可能难以为继。这引领函数式编程进入了”文艺复兴“时期，许多编程语言都包含来自函数式编程中的结构成分，**因为这些成分可以更好地帮助分析推导并发和并行应用程序中的问题**。

函数式编程的本质：洞察到程序实际上可以按照纯粹的数学函数来编写；也就是说，每次给这些函数传递相同的输入时，它们将总是返回相同的值，并且不会产生副作用。在函数式编程中编写代码类似于在数学中组合函数。

争用是在多核心cpu上运行的代码最大的性能杀手

### 不可变性

当一个变量可在不同时刻指向不同的值时，它就被称为具有可变的状态。对不可变的数据执行任何操作都将创建一个新的数据结构，其保存了更改后的结果。 

最好使用编译器而不是约定来强制不可变性，这也就意味着将值传递给构造函数，而不是调用setter，并使用编程语言的特性，如Java中的final 以及Scala 中的val。java 并不会让一切都默认不可变，（所以通常要动点手脚），Scala的case class 则默认提供了不可变性。

如果将一个表达式替换为其求值后的结果，对程序（其它部分）的执行不产生影响，这个表达式便可称为引用透明。例如在不可变列表中添加、删除或者更新一个值的行为，将会产生一个具有修改后的值的新列表，程序中任何仍然使用该原始列表的部分都不会看到对该列表的更改。PS：**从中可以看到，不可变/引用透明有助于提高 可以并行执行的代码比例**。

### 函数作为第一公民

函数作为第一公民 目的是当让代码根据组合性。

## 左耳听风

20189.29 补充：来自《左耳听风》课程

在编程这个世界中，更多的编程工作是解决业务上的问题，而不是计算机的问题。所以内存操作 等这些事情 尽量不要反应到 业务代码上来。

![](/public/upload/architecture/function_programming.png)

代码 = 控制 + 逻辑，代码在描述 你要干什么，而不是怎么干。map/reduce 是控制，toUpper/sum 是业务逻辑。函数式编程固化/隐藏了“控制”代码，使得“逻辑” 代码的编写显式化了。

在皓哥文章末的评论中，有用户提到：面向对象编程和函数式编程 他们的关注点不一样，面向对象编程 帮助你设计更复杂的应用程序，函数式编程帮助你简化更复杂的计算。所以，**我们学东西不是为了腾笼换鸟，而是胸有谋划，师夷长技。**

### 柯里化

	def inc(x):
		def incx(y):
			return x+y
		return incx
	
	incc = inc(2)
	inc5 = inc(5)
	
	print inc2(5)	// output 7
	print inc5(5) // output 10
	
大牛 **从参数分解的角度** 对柯里化 的描述是：将一个函数的多个参数 分解成多个函数，然后将函数多层封装起来，每层函数都返回一个函数去接收下一个参数，这样可以简化函数的多个参数。 

### 装饰器

皓哥用专门一章讲了装饰器， 从全文看，比较好玩的是皓哥如何将装饰器模式、linux 管道、代码层面的编排、pipeline 联系到一起的。

shell 命令

	ps auwwx | awk '{print $2}' | sort -n | xargs echo
	
抽象成函数式的样子（函数嵌套）

	xargs(echo,sort(n,awk('print $2',ps(auwwx))))
	
也可以将函数放进数组里，此处可能需要对三个方法进行修饰（Decorator）， 以便将普通函数管道化

	pids = for_each(result,[ps_auwwx,awk_p2,sort_n,xargs_echo])
	
如果我们将这些函数当做微服务，那么管道其实在做服务的编排（再往下有类的编排、方法的编排）。

对于pipeline 式的代码，体现在java中便需要定义pipeline、Step/Stage等接口，然后自定义逻辑实现Step/Stage接口（如果逻辑是已经写好的，还需Decorator 修一下），最后由pipeline 驱动执行。而函数作为“第一公民”后，设计模式的三大类创建、行为、结构中的行为型模式 都可以由函数嵌套、函数参数、函数返回值等直接实现（函数层面），无需上升java的类/接口层面了。换句话说，**设计模式由类/接口层面（java 提到设计模式都会有一个复杂的类图） 下沉到 函数层面了**，将一些功能或逻辑代码 通过函数的拼装（因此才有了柯里化 和 高阶函数等）来组织。这也可说是 函数式编程的一个体现了吧。

我们弄个小节专门讲下

## 基本概念

|||函数的概念|其它|
|---|---|---|---|
|函数式编程||一种东西和另一种东西之间的对应关系。|至于什么柯里化、什么数据不可变，都只是外延体现而已。|
|命令式编程|面向过程、面向对象等|一个步骤||

	object HelloWorld  {  
	  def main(args: Array[String]): unit = {  
	    args.filter( (arg:String) => arg.startsWith("G") )  
	        .foreach( (arg:String) => Console.println("Found " + arg) )  
	  }  
	} 
	
函数式的代码是“对映射的描述”，查看scala中filter函数的源码，或许更能体会对映射的描述的感觉。

	class Array[A]{  
	    // ...  
	   def filter  (p : (A) => Boolean) : Array[A] = ... // not shown  
	} 


[什么是函数式编程思维？](https://www.zhihu.com/question/28292740/answer/100284611)

当我在说函数也是一个对象时，我在说什么：函数作为参数进行传递、把它们存贮在变量中、或者当作另一个函数的返回值

**函数不是方法**，方法指的是类或对象中定义的函数，**方法中的this引用会隐性的指向某一对象**。比如`(s:String) => s.toUpperCase()`便是一个函数，它是独立的，没有通过this与一个对象相关联。函数不是方法，但在某些时候，会将方法归入函数。

scala函数中，有偏函数一说，限定了函数的输入。还有柯里化函数，A way to write functions with multiple parameter lists. For instance  
`def f(x: Int)(y: Int)` is a curried function with two parameter  
lists. A curried function is applied by passing several arguments  
lists, as in: f(3)(4). However, it is also possible to write a partial  
application of a curried function, such as f(3).  

比如`"hello world"`可以称为String字面量，那么`(s:String) => s.toUpperCase`可以称为函数字面量。

函数没有副作用，比如没有全局对象被修改。当一个函数采用其它函数作为变量或返回值时，它被称为高阶函数。

函数可以内嵌。

一个方法的极简定义`def id = Match.random`，完整形式`def def():Unit = Match.random`

定一个函数类型的值（注意是函数不是方法）`var greetVar:String => String = (name) => "Hello" + name`， 分为三个部分
 
1. var greetVar
2. 类型 String => String
3. "Hello" + name

一个方法可以赋给一个函数类型的变量`val gr = greet _`，此处greet是一个方法名。

在该小节中，笔者因为水平和对scala的认识有限，还未能精确描述函数式编程的精髓

1. 从基本组成和相互关系的角度来描述，面向对象的基本组成是类和对象，基本组成的相互关系是继承、聚合等。函数式编程基本组成是函数、偏函数等，函数与函数关系可以是调用、内嵌、返回等。**当然，面向对象和函数编程是两个维度的东西，对比不太合适。**
2. 笔者学习spark的时候，了解到，你对data set的一系列函数式调用，并不会被立即执行。比如对一个数据集，你先过滤男性、又将值翻番。从执行角度看，不会是把数据拿出来，先for循环一把过滤，再for循环一把每个值乘以2。一系列的函数调用更像是一个执行计划，spark会优化这些计划的执行，最终可能一次for循环就好了。

## 函数式编程对java设计模式的影响——refactoring object-oriented design patterns with lambdas

以前分析java 项目，通常会有一个复杂的类图 描述基本抽象及依赖关系。那么通过高阶函数，可以减少类的数量，依赖关系直接成了参数。因此，高阶函数 有利于 简化复杂项目类图的 层次 及 一些边缘抽象。 

函数式编程 对传统设计模式的影响（这是个大话题）

1. 很多角色 不需要专门的类，比如jib 中Observer 就使用jdk8 自带的Consumer 替代了。
2. 高阶函数：一个类本来可以有很多方法，现在都一个主要方法（执行主流程），然后传入Function、Consumer（表示策略） 代替了，模板模式、策略模式基本都消亡了。
4. 逻辑聚合越来越普遍了，以前只是数据聚合（比如一个配置类聚合其它配置类，形成一个更大的配置类）。**jib-core 很多地方拿Runnable 当成员到处传着玩**。反过来说，逻辑更容易被拆分，参见`Consumer.andThen`

		@FunctionalInterface
		public interface Consumer<T> {
		    void accept(T t);
		    default Consumer<T> andThen(Consumer<? super T> after) {
		        Objects.requireNonNull(after);
		        return (T t) -> { accept(t); after.accept(t); };
		    }
		}

此外，从jib-core event 设计中还可以看到一点，**用更多Functional interface 对象 替代 if else 逻辑**，比如 Jib 中的Handler 只会处理一个特定类型的事件。代码中会有更多的对象、funciton，但每个对象和function 都更简单。

[How Functional Programming will (Finally) do Away With the GoF Patterns](https://blog.jooq.org/2016/07/04/how-functional-programming-will-finally-do-away-with-the-gof-patterns/)

1. A lot of the GoF design patterns stem from a time when EVERYTHING needed to be an object. Object orientation was the new holy grail, and people even wanted to push objects down into databases. Object databases were invented (luckily, they’re all dead) and the SQL standard was enhanced with ORDBMS features. 面向对象领域，“一切皆对象”是最高准则，人们甚至想把对象存到数据库里去。
2. Since Java 8, finally, we’re starting to recover from the damage that was made in early days of object orientation in the 90s, and we can move back to **a more data-centric, functional, immutable programming model** where data processing languages like SQL are appreciated rather than avoided, and Java will see more and more of these patterns, hopefully.

这部分内容非常重要，在[函数式编程的设计模式](http://qiankunli.github.io/2018/12/15/functional_programming_patterns.html) 有专门阐述。

## 一些代码技巧

在java8 的List 接口中，存在一个default method 

	void sort(Comparator<? super E> c)
	
对应`java.util.Collections` 中的sort 方法


	 public static <T> void sort(List<T> list, Comparator<? super T> c) {
        list.sort(c);
    }
    
从[编程的本质](http://qiankunli.github.io/2018/07/14/nature_of_code.html) 中 可以知道 程序 = 控制 + 逻辑（这与函数式编程理念是非常契合的）。在这里的sort方法中，排序是用冒泡还是插入是控制 ，与业务无关。而Comparator 描述的是逻辑，与业务紧密相关。 

在 [异步编程——Promise](https://github.com/hprose/hprose-java/wiki/%E5%BC%82%E6%AD%A5%E7%BC%96%E7%A8%8B%E2%80%94%E2%80%94Promise) 中作者提了三个接口

	interface Callback<R, V> {}
	// 对输入采取一定的动作，没有返回
	public interface Action<V> extends Callback<Void, V> {
	    void call(V value) throws Throwable;
	}
	// 将输入转换为输出
	public interface Func<R, V> extends Callback<R, V> {
    	R call(V value) throws Throwable;
	}
	// 将输入转换为输出，异步
	public interface AsyncFunc<R, V> extends Callback<R, V> {
    	Promise<R> call(V value) throws Throwable;
	}
	
实现一个完全符合函数式编程理念的项目很难，通常也很少有这样的机会。但在我们日常的代码中，多用用Action、Func、AsyncFunc 这类接口，却可以做到，可以在很大程度上提高代码的可读性。

