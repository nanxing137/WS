## 建造者模式
##### 建造者模式（Builder Pattern）使用多个简单的对象一步一步构建成一个复杂的对象。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
##### 一个 Builder 类会一步一步构造最终的对象。该 Builder 类是独立于其他对象的。

#### 介绍
意图：将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。  
主要解决：主要解决在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起的算法却相对稳定。    
何时使用： **一些基本部件不会变，而其组合经常变化的时候。**  
如何解决：将变与不变分离开。    
关键代码：建造者：创建和提供实例，导演：管理建造出来的实例的依赖关系。    
角色：
1. builder：为创建一个产品对象的各个部件指定抽象接口。
2. ConcreteBuilder：实现Builder的接口以构造和装配该产品的各个部件，定义并明确它所创建的表示，并提供一个检索产品的接口。
3. Director：构造一个使用Builder接口的对象。
4. Product：表示被构造的复杂对象。ConcreteBuilder创建该产品的内部表示并定义它的装配过程，包含定义组成部件的类，包括将这些部件装配成最终产品的接口。

优点： 
1. 建造者独立，易扩展。 
2. 便于控制细节风险。 

缺点： 
1. 产品必须有共同点，范围有限制。
2. 如内部变化复杂，会有很多的建造类

使用场景： 
1. 需要生成的对象具有复杂的内部结构。
2. 需要生成的对象内部属性本身相互依赖。 
注意事项：与工厂模式的区别是：**建造者模式更加关注与零件装配的顺序**。    
#### 实现：
**听说最近人工智能可以写作了，那么这里我们也写一个能自动造句的人工~~智障~~智能，叫做鹏酱。鹏酱可以造主谓宾结构的句子。代码如下：**
##### 一、建造变动较小较慢的组件
1.主语系列（subject）

```
/**
 * 
 * @author Thornhill
 * @see 主语的接口
 */
public interface Subject {
	public void showSbuject();
}

/**
 * 
 * @author Thornhill
 * @see 主语“做饭”
 */
public class Cook implements Subject {

	@Override
	public void showSbuject() {
		System.out.print("做饭");
	}

}

/**
 * 
 * @author Thornhill
 * @see 主语“编程”
 */
public class Coding implements Subject {

	@Override
	public void showSbuject() {
		System.out.print("编程");
	}

}
```
2.谓语系列（predicate）

```
/**
 * 
 * @author Thornhill
 * @see 谓语接口
 */
public interface Predicate {
	public void showPredicate();
}


/**
 * 
 * @author Thornhill
 * @see 谓语“是”
 */
public class Is implements Predicate {

	@Override
	public void showPredicate() {
		System.out.print("是");
	}

}

/**
 * 
 * @author Thornhill
 * @see 谓语“不是”
 */
public class IsNot implements Predicate {

	@Override
	public void showPredicate() {
		System.out.print("不是");
	}

}

```

3.宾语系列（object）

```
/**
 * 
 * @author Thornhill
 * @see "宾语接口"
 */
public interface Object {
	public void showObject();
}

/**
 * 
 * @author Thornhill
 * @see 宾语“简单”
 */
public class Easy implements Object {

	@Override
	public void showObject() {
		System.out.println("简单的");

	}

}

/**
 * 
 * @author Thornhill
 * @see 宾语“困难”
 */
public class Difficult implements Object {

	@Override
	public void showObject() {
		System.out.println("困难的");		
	}

}
```
#### 二、建造不会变动的产品（Product）框架

```
/**
 * 
 * @author Thornhill
 * @see 产品框架（Product）
 */
public class ChickenSoup {
	private Subject subject;
	private Predicate predicate;
	private Object object;

	public Subject getSubject() {
		return subject;
	}

	public void setSubject(Subject subject) {
		this.subject = subject;
	}

	public Predicate getPredicate() {
		return predicate;
	}

	public void setPredicate(Predicate predicate) {
		this.predicate = predicate;
	}

	public Object getObject() {
		return object;
	}

	public void setObject(Object object) {
		this.object = object;
	}

	public void showChickenSoup() {
		subject.showSbuject();
		predicate.showPredicate();
		object.showObject();
	}
}
```
#### 三、建造Builder接口：

```
/**
 * 
 * @author Thornhill
 * @see 鸡汤文章的接口（Builder）
 */
public interface EssayBuilder {

	public void setEssaySubject();

	public void setEssayPredicate();

	public void setEssayObject();

	public ChickenSoup getEssay();
}
```
#### 四、建造实现Builder接口的具体生成器（ConcreteBuilder）：
```
/**
 * 
 * @author Thornhill
 * @see 构造“做饭简单”的具体生成器
 */
public class CookingIsEasy implements EssayBuilder {

	private ChickenSoup chickenSoup = new ChickenSoup();

	@Override
	public void setEssaySubject() {
		chickenSoup.setSubject(new Cook());

	}

	@Override
	public void setEssayPredicate() {
		chickenSoup.setPredicate(new Is());
	}

	@Override
	public void setEssayObject() {
		chickenSoup.setObject(new Easy());

	}

	@Override
	public ChickenSoup getEssay() {
		return chickenSoup;
	}

}

/**
 * 
 * @author Thornhill
 * @see 构造“编程不难”的具体生成器
 */
public class CodingIsNotDiffcult implements EssayBuilder {

	private ChickenSoup chickenSoup = new ChickenSoup();

	@Override
	public void setEssaySubject() {
		chickenSoup.setSubject(new Coding());

	}

	@Override
	public void setEssayPredicate() {
		chickenSoup.setPredicate(new IsNot());

	}

	@Override
	public void setEssayObject() {
		chickenSoup.setObject(new Difficult());

	}

	@Override
	public ChickenSoup getEssay() {
		return chickenSoup;
	}

}
```
#### 五、建造通过Builder接口，按照顺序调用ConcreteBuilder的导演（Director）：

```
/**
 * 
 * @author Thornhill
 * @see 调用具体生成器的“导演”类
 */
public class EssayDirector {
	public ChickenSoup getFinalEssay(EssayBuilder eb) {
		eb.setEssayObject();
		eb.setEssayPredicate();
		eb.setEssaySubject();
		return eb.getEssay();
	}
}
```
##### 六、测试：

```
/**
 * 
 * @author Thornhill
 * @see 建造者模式测试
 */
public class Test {

	public static void main(String[] args) {
		ChickenSoup essay1 = new EssayDirector().getFinalEssay(new CookingIsEasy());
		essay1.showChickenSoup();
		
		ChickenSoup essay2 = new EssayDirector().getFinalEssay(new CodingIsNotDiffcult());
		essay2.showChickenSoup();
		
	}

}
```
**结果**：

```
做饭是简单的
编程不是困难的
```
####结语：
##### 建造者模式适用于组建不变而组合常有变动的情景。产品（Product），抽象接口（Builder），导演（Director）通常是不用变的部分；而具体生成器（ConcreteBuilder）是扩展时增加的部分，通常改动只改动具体生成器。
[GitHub源代码](https://github.com/thornshell/Code)


