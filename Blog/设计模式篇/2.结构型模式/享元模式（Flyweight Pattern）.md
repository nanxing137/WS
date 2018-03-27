# 享元模式

**享元模式（Flyweight Pattern）主要用于减少创建对象的数量，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式。**

**享元模式尝试重用现有的同类对象，如果未找到匹配的对象，则创建新对象。** 

---

## 介绍

**意图：**运用共享技术有效地支持大量细粒度的对象。

**主要解决：**在有大量对象时，有可能会造成内存溢出，我们把其中共同的部分抽象出来，如果有相同的业务请求，直接返回在内存中已有的对象，避免重新创建。

**何时使用：** 1、系统中有大量对象。 2、这些对象消耗大量内存。 3、这些对象的状态大部分可以外部化。 4、这些对象可以按照内蕴状态分为很多组，当把外蕴对象从对象中剔除出来时，每一组对象都可以用一个对象来代替。 5、系统不依赖于这些对象身份，这些对象是不可分辨的。

**如何解决：**用唯一标识码判断，如果在内存中有，则返回这个唯一标识码所标识的对象。

**关键代码：**用 HashMap 存储这些对象。

**应用实例：** 1、JAVA 中的 String，如果有则返回，如果没有则创建一个字符串保存在字符串缓存池里面。 2、数据库的数据池。

**优点：**大大减少对象的创建，降低系统的内存，使效率提高。

**缺点：**提高了系统的复杂度，需要分离出外部状态和内部状态，而且外部状态具有固有化的性质，不应该随着内部状态的变化而变化，否则会造成系统的混乱。

**使用场景：** 1、系统有大量相似对象。 2、需要缓冲池的场景。

**注意事项：** 1、注意划分外部状态和内部状态，否则可能会引起线程安全问题。 2、这些类必须有一个工厂对象加以控制。

---

## 实现

**我们模拟户籍制度，将使用享元模式共享使用地区（Area）**

#### 步骤一

**创建一个地区接口(AbsArea)：**

```java
/**
 * @see AbsArea抽象地区定义输出地区的行为
 * @author Thornhill
 *
 */
public interface AbsArea {
	public void shwo();
}
```

#### 步骤二

**创建实现接口的地区类(Area)：**

```java
/**
 * @see 因为要使用享元模式，防止放在HashMap中的元素被破坏，其中AREANAME需要为private+final，确保不会被修改
 * @author Thornhill
 *
 */
public class Area implements AbsArea {
	final private String AREANAME;

	public Area(String AREANAME) {
		this.AREANAME = AREANAME;
	}

	@Override
	public void shwo() {
		System.out.println("户籍所在地为" + AREANAME);

	}

}
```



#### 步骤三

**创建享元模式工厂，根据给定的地区返回地区的对象：**

```java
/**
 * 享元模式工厂，持有一个MAP，调用时检查MAP中是否持有现有对象 
 * 如果有，直接返回此对象
 * 没有，加入到MAP中，并返回此对象
 * @author Thornhill
 *
 */
public class ShapeFactory {
	private static final Map<String, AbsArea> MAP = new HashMap<>();

	public static AbsArea getArea(String AREANAME) {
		AbsArea absArea = MAP.get(AREANAME);
		if (null == absArea) {
			absArea = new Area(AREANAME);
			MAP.put(AREANAME, absArea);
		}
		return absArea;
	}
}
```

#### 步骤四

**使用该工厂，通过传入地区名来获取地区对象：**

```java
public class FlyweightPatternDemo {

	public static void main(String[] args) {
		AbsArea absArea1 = AreaFactory.getArea("内蒙古");
		AbsArea absArea2 = AreaFactory.getArea("内蒙古");
		AbsArea absArea3 = AreaFactory.getArea("广东");
		AbsArea absArea4 = AreaFactory.getArea("重庆");

		absArea1.shwo();
		absArea2.shwo();
		absArea3.shwo();
		absArea4.shwo();

		System.out.println(absArea1 == absArea2);
		System.out.println(absArea3 == absArea4);

	}

}
```



输出两两的比较结果是为了检查享元模式是否起作用，即相同的地区，对象是否相同。

#### 步骤五

**验证输出：**

```java
户籍所在地为内蒙古
户籍所在地为内蒙古
户籍所在地为广东
户籍所在地为重庆
true
false
```

---

### 结语：
**享元模式要保证共享的元素不被其他角色修改，否则将影响其他一起共享此元素的对象正确性，也破坏了享元工厂中的MAP。**


[GitHub源代码](https://github.com/thornshell/Code)