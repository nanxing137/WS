## 适配器模式
##### 适配器模式（Adapter Pattern）是作为两个不兼容的接口之间的桥梁。这种类型的设计模式属于结构型模式，它结合了两个独立接口的功能。
##### 这种模式涉及到一个单一的类，该类负责加入独立的或不兼容的接口功能。举个真实的例子，读卡器是作为内存卡和笔记本之间的适配器。您将内存卡插入读卡器，再将读卡器插入笔记本，这样就可以通过笔记本来读取内存卡。

---
### 介绍
##### 意图：将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
##### 主要解决：主要解决在软件系统中，常常要将一些"现存的对象"放到新的环境中，而新环境要求的接口是现对象不能满足的。
##### 何时使用： 
1. 系统需要使用现有的类，而此类的接口不符合系统的需要。 
2. 想要建立一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作，这些源类不一定有一致的接口。
3. 通过接口转换，将一个类插入另一个类系中。（比如老虎和飞禽，现在多了一个飞虎，在不增加实体的需求下，增加一个适配器，在里面包容一个虎对象，实现飞的接口。）
##### 如何解决：继承或依赖（推荐）。
##### 关键代码：适配器继承或依赖已有的对象，实现想要的目标接口。

---
### 实现：
##### 举个例子：
**就说220V接到5V的充电器**  
##### 一、首先我们有220V电压的插口
```
/**
 * @see 提供原始电压，220V
 * @author Thornhill
 *
 */
public class Voltage220V {
	public final Integer VOLTAGE = 220;

	public void output220V() {
		System.out.println("输出" + VOLTAGE + "V电压");
	}
}
```
##### 二、我们需要实现5V电压手机的接口
```
/**
 * @see 手机需要提供5V的电压
 * @author Thornhill
 *
 */
public interface Voltage5V {
	public void output5V();
}
```
##### 三、创建适配器，其中有两种方法：
1. 类适配器模式：

```
import phone.Voltage5V;
import powerSupply.Voltage220V;
/**
 * @see	类适配器模式，继承父类，实现接口，完成适配
 * @author Thornhill
 *
 */
public class Adapter1 extends Voltage220V implements Voltage5V {

	@Override
	public void output5V() {
		System.out.println("输出" + super.VOLTAGE / 44 + "V电压");

	}

}
```
**优点：**
- 由于是继承，灵活性增强，可重写父类中的方法。  

**缺点：**
- Java单继承，使用继承将会给必要的其他继承带来问题。比如要求另一个适配对象必须为接口。
- 不恰当的使用继承。不符合李氏代换原则。代码逻辑性差。
- 将父类中的方法直接暴露在适配器中。安全性降低。耦合性增强。
2. 对象适配器模式：
```
import phone.Voltage5V;
import powerSupply.Voltage220V;
/**
 * @see	对象适配模式，持有 一个类，实现类接口，完成适配。
 * @author Thornhill
 *
 */
public class Adapter2 implements Voltage5V {
	private Voltage220V voltage220V = new Voltage220V();

	@Override
	public void output5V() {
		System.out.println("输出" + voltage220V.VOLTAGE / 44 + "V电压");
	}

}
```
**优点：**
- 使用灵活，不限制继承。
- 使用组合而不是继承，更加符合复用原则。
- 屏蔽包含对象的据实现。安全性提高，耦合降低。

**缺点：**
- 暂无。（作者没想到）

##### 五、创建客户端使用

```
import adapter.Adapter1;
import adapter.Adapter2;
import phone.Voltage5V;

/**
 * @see 创建客户端使用
 * @author Thornhill
 *
 */
public class Client {

	public static void main(String[] args) {
		Voltage5V voltage5v1 = new Adapter1();
		Voltage5V voltage5v2 = new Adapter2();
		voltage5v1.output5V();
		voltage5v2.output5V();
	}

}
```
##### 输出结果：

```
输出5V电压
输出5V电压
```


### 结语：
**适配器不是在详细设计时添加的，而是解决正在服役的项目的问题。**
**适配器模式顾名思义，适用于在不匹配的问题已经发生了，需要调节的场合。不要过多的使用适配器模式。将会给代码可读性和后期的维护带来灾难。**   

[GitHub源代码](https://github.com/thornshell/Code)