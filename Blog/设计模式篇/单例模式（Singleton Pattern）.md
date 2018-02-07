## 单例模式
#### 单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
#### 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。
###### 注意：
1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

#### 介绍
**意图：** 保证一个类仅有一个实例，并提供一个访问它的全局访问点。
**主要解决：一个全局使用的类频繁地创建与销毁。  
**何时使用：** 当您想控制实例数目，节省系统资源的时候。    
**如何解决：** 判断系统是否已经有这个单例，如果有则返回，如果没有则创建。    
**关键代码：构造函数是私有的。**    
**应用实例：** 一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。        
**优点：** 
1. 在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
2.  避免对资源的多重占用（比如写文件操作）。 

**缺点：** 没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。
使用场景：        
1. 要求生产唯一序列号。 
2. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。 
3. 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。
注意事项：getInstance() 方法中需要使用同步锁 synchronized (Singleton.class) 防止多线程同时进入造成 instance 被多次实例

#### 实现：我们就以打印机（Printer）为例
##### 1、懒汉式，线程不安全
是否 Lazy 初始化：是    
是否多线程安全：否  
实现难度：易    
描述：这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。    
* 这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。    
> 代码实例：

```
public class Printer1 {
	private static Printer1 printer;

	private Printer1() {};

	public static Printer1 getPrinter() {
		if (null != printer) {
			printer = new Printer1();
		}
		return printer;
	}
}
```
**这个方法可以说是非常懒汉了，单线程的首选。**
##### 接下来介绍的几种实现方式都支持多线程，但是在性能上有所差异。
#### 2、懒汉式，线程安全
是否 Lazy 初始化：是    
是否多线程安全：是  
实现难度：易    
描述：这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是，效率很低，99% 情况下不需要同步。    
优点：第一次调用才初始化，避免内存浪费。    
缺点：必须加锁 synchronized才能保证单例，但加锁会影响效率。
* getInstance() 的性能对应用程序不是很关键（该方法使用不太频繁）。
> 代码实例：

```
public class Printer2 {
	private static Printer2 printer;

	private Printer2() {}

	public static synchronized Printer2 getPrinter() {
		if (null != printer) {
			printer = new Printer2();
		}
		return printer;
	}
}
```
*强烈不推荐使用*     
**这种方法问题很大，虽然解决了多线程中同步的问题，但是事实上，同步只有在new第一个实例时候才有意义。但因为有这个锁，严重拖慢了后续的运行。**
#### 3、饿汉式
是否 Lazy 初始化：否    
是否多线程安全：是  
实现难度：易    
描述：这种方式比较常用，但容易产生垃圾对象。    
优点：没有加锁，执行效率会提高。    
缺点：类加载时就初始化，浪费内存。  
* 它基于 classloder 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，虽然导致类装载的原因有很多种，在单例模式中大多数都是调用 getInstance 方法， 但是也不能确定有其他的方式（或者其他的静态方法）导致类装载，这时候初始化 instance 显然没有达到 lazy loading 的效果。
> 代码实例：

```
public class Printer3 {
	private static Printer3 printer = new Printer3();

	private Printer3() {};

	public static Printer3 getPrinter() {
		return printer;
	}
}
```
**这种饥汉式的方法已经是单例模式中的上策，运行速度最快。不在意有可能提前占用空间的话，放心地用吧。**
#### 4、双检锁/双重校验锁（DCL，即 double-checked locking）
JDK 版本：JDK1.5 起 
是否 Lazy 初始化：是    
是否多线程安全：是  
实现难度：较复杂    
描述：这种方式采用双锁机制，安全且在多线程情况下能保持高性能。  

优点：
1. 在上锁的同时还能保证较高的执行效率。
2. ~~实现方法高大上，装逼必备。~~

* getInstance() 的性能对应用程序很关键。
> 代码实例：

```
public class Printer4 {
	private static Printer4 printer = null;

	private Printer4() {};

	public static Printer4 getPrinter() {
		if (null == printer) {
			synchronized (Printer4.class) {
				if (null == printer) {
					printer = new Printer4();
				}
			}
		}
		return printer;
	}
}
```
**可以说从外观上就可以发现这个代码不一般了。这个方式可以一定程度上解决线程同步问题。不过也有人说这种方式会因为指令重排而产生空指针异常的问题。**
#### 5、登记式/静态内部类
是否 Lazy 初始化：是    
是否多线程安全：是  
实现难度：一般  
描述：这种方式能达到双检锁方式一样的功效，但实现更简单。对静态域使用延迟初始化，应使用这种方式而不是双检锁方式。这种方式只适用于静态域的情况，双检锁方式可在实例域需要延迟初始化时使用。    
这种方式同样利用了 classloder 机制来保证初始化 printer 时只有一个线程，它跟第 3 种方式不同的是：第 3 种方式只要 Printer3 类被装载了，那么 printer 就会被实例化（没有达到 lazy loading 效果），而这种方式是 Printer5 类被装载了，printer 不一定被初始化。因为 Printer5Holder 类没有被主动使用，只有通过显式调用 getPrinter 方法时，才会显式装载 Printer5Holder 类，从而实例化 printer。想象一下，如果实例化 printer 很消耗资源，所以想让它延迟加载，另外一方面，又不希望在 Printer5 类加载时就实例化，因为不能确保 Printer5 类还可能在其他的地方被主动使用从而被加载，那么这个时候实例化 printer 显然是不合适的。这个时候，这种方式相比第 3 种方式就显得很合理。    
> 代码实例：

```
public class Printer5 {
	private static class Printer5Holder {
		private static final Printer5 PRINTER = new Printer5();
	}

	private Printer5() {};

	public static final Printer5 getPrinter() {
		return Printer5Holder.PRINTER;
	}
}
```
**这种方法可以严格遵守lazy loading，如果有lazy loading的硬性要求，那静态内部类是个不错的选择。**
#### 6、枚举
JDK 版本：JDK1.5 起 
是否 Lazy 初始化：否    
是否多线程安全：是  
实现难度：易    
描述：这种实现方式还没有被广泛采用，但这是实现单例模式的最佳方法。它更简洁，自动支持序列化机制，绝对防止多次实例化。    
* 这种方式是 Effective Java 作者 Josh Bloch 提倡的方式，它不仅能避免多线程同步问题，而且还自动支持序列化机制，防止反序列化重新创建新的对象，绝对防止多次实例化。不过，由于 JDK1.5 之后才加入 enum 特性，用这种方式写不免让人感觉生疏，在实际工作中，也很少用。
* 不能通过 reflection attack 来调用私有构造方法。（不会被反射攻击） 
> 代码实例：
```
public enum Printer6 {
	PRINTER;
	public void operate() {
		// 在这里实现一些为所欲为的方法
	}
}
```
**简单粗暴，高端高效，还能阻挡反射攻击，枚举类型，就是好！**

#### 结语：
##### 一般情况下，不建议使用第 1 种和第 2 种懒汉方式，建议使用第 3 种饿汉方式。只有在要明确实现 lazy loading 效果时，才会使用第 5 种登记方式。如果涉及到反序列化创建对象时，可以尝试使用第 6 种枚举方式。如果有其他特殊的需求，可以考虑使用第 4 种双检锁方式。
[GitHub源代码](https://github.com/thornshell/Code)
