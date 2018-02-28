## 原型模式
##### 原型模式（Prototype Pattern）是用于创建重复的对象，同时又能保证性能。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
##### 这种模式是实现了一个原型接口，该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式。例如，一个对象需要在一个高代价的数据库操作之后被创建。我们可以缓存该对象，在下一个请求时返回它的克隆，在需要的时候更新数据库，以此来减少数据库调用。

#### 介绍
#### 意图：用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
#### 主要解决：在运行期建立和删除原型。
#### 何时使用： 
1. 当一个系统应该独立于它的产品创建，构成和表示时。
2. 当要实例化的类是在运行时刻指定时，例如，通过动态装载。
3. 为了避免创建一个与产品类层次平行的工厂类层次时。 
4. 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。
#### 如何解决：利用已有的一个原型对象，快速地生成和原型对象一样的实例。
#### 关键代码： 1、实现克隆操作，在 JAVA 继承 Cloneable，重写 clone()，在 .NET 中可以使用 Object 类的 MemberwiseClone() 方法来实现对象的浅拷贝或通过序列化的方式来实现深拷贝。 2、原型模式同样用于隔离类对象的使用者和具体类型（易变类）之间的耦合关系，它同样要求这些"易变类"拥有稳定的接口。
#### 应用实例： 
1. 细胞分裂。
2. JAVA 中的 Object clone() 方法。
#### 优点： 
1. 性能提高。
2. 逃避构造函数的约束。
#### 缺点： 
1. 配备克隆方法需要对类的功能进行通盘考虑，这对于全新的类不是很难，但对于已有的类不一定很容易，特别当一个类引用不支持串行化的间接对象，或者引用含有循环结构的时候。
2. 必须实现 Cloneable 接口。
3. 逃避构造函数的约束。
#### 使用场景： 
1. 资源优化场景。 
2. 类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
3. 性能和安全要求的场景。
4. 通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
5. 一个对象多个修改者的场景。
6. 一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。
7. 在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过 clone 的方法创建一个对象，然后由工厂方法提供给调用者。原型模式已经与 Java 融为浑然一体，大家可以随手拿来使用。
注意事项：与通过对一个类进行实例化来构造新对象不同的是，原型模式是通过拷贝一个现有对象生成新对象的。浅拷贝实现 Cloneable，重写，深拷贝是通过实现 Serializable 读取二进制流。
### 实现方案：
**具体实现分为浅拷贝和深拷贝**  
浅拷贝（shallow copy）

　　被复制对象的所有变量都含有与原来的对象相同的值(仅对于简单的值类型数据)，而所有的对其他对象的引用都仍然指向原来的对象。换言之，只负责克隆按值传递的数据（比如：基本数据类型、String类型）。  
　　**简而言之就是只创建了栈中的指针（有些是指针，有些直接是数据），而两个指针指向的堆中的地址是一样的。即没有创建真实的对象，改变其中的某一个，另一个也会相应的改变，因为这就是一个对象。但有些数据类型在栈中存储，那么浅拷贝就可以实现。**    
　　在Java中object实现了浅拷贝。所以所有的对象直接调用super.clone()即可。  
深拷贝 （deep copy）

　　被复制对象的所有的变量都含有与原来的对象相同的值，除去那些引用其他对象的变量。那些引用其他对象的变量将指向被复制过的新对象，而不再是原有的那些被引用的对象。换言之，除了浅度克隆要克隆的值外，还负责克隆引用类型的数据，基本上就是被克隆实例所有的属性的数据都会被克隆出来。    
　　**简而言之，除了栈中的指针（有些是指针，有些直接是数据），堆中的具体对象也会被克隆一边。即堆栈都是新数据。所以各自发生的修改不会影响到对方。**  
　　Java中，深度复制只需实现CloneAble接口，实现clone函数，具体情况具体分析即可。
　　
### 模式结构

1. 简单原型模式：用于原型的版本不多的时候
![简单原型模式](https://images0.cnblogs.com/blog/449064/201410/252102442157022.jpg)
2. 登记模式的原型模式：如果原型的实现很多种版本，那么通过一个登记管理类，可以方便的实现原型的管理。
![登记模式的原型模式](https://images0.cnblogs.com/blog/449064/201410/252103274656075.jpg)
## 实现：我们已细胞的分裂为例子：

---

#### 一、创建一个抽象原型（Prototype）：
```
/**
 * @see 直接继承Java的Cloneable即可
 * @author Thornhill
 *
 */
public interface Splittable extends Cloneable {

	public Splittable clone() throws CloneNotSupportedException;

	public void showSelf();

}
```
如果有更多的功能，可以加在此接口中  

---

#### 二、创建具体原型（Concrete Prototype）：
```
import prototype.Splittable;

/**
 * @see 肌肉细胞
 * @author Thornhill
 *
 */
public class MuscleCells implements Splittable {

	private String cellType = "肌肉细胞";
	private String cellName;
	private String cellLocation;

	public MuscleCells(String cellName, String cellLocation) {
		this.cellName = cellName;
		this.cellLocation = cellLocation;
	}

	public Splittable clone() {
		return new MuscleCells(cellName, cellLocation);
	}

	public String getCellName() {
		return cellName;
	}

	public void setCellName(String cellName) {
		this.cellName = cellName;
	}

	public String getCellLocation() {
		return cellLocation;
	}

	public void setCellLocation(String cellLocation) {
		this.cellLocation = cellLocation;
	}

	@Override
	public void showSelf() {
		System.out.println(cellType + " " + cellName + " " + cellLocation);
	}
}
```
实际情况是非常复杂的，原型模式就是为了解决重新创建的巨大开销和冗余代码。

---

#### 三、创建原型管理器（Prototype Manager）：

```
import prototype.Splittable;

public class CellsManager {
	private static Map<String, Splittable> map = new HashMap<String, Splittable>();

	private CellsManager() {
	};

	public static Splittable getCells(String cellType) {
		Splittable cell = map.get(cellType);
		return cell;
	}

	public static void putCells(String cellType, Splittable cell) {
		map.put(cellType, cell);
	}

	public static void removeCells(String cellType) {
		map.remove(cellType);
	}
}
```
纯静态类，具体功能为原型的管理（增删查）。

---

#### 四、创建客户（Client），即使用者：
```
import concretePrototype.MuscleCells;
import prototype.Splittable;
import prototypeManager.CellsManager;

public class Client {

	public static void main(String[] args) throws CloneNotSupportedException {
		/**
		 * 创建一个肌肉细胞，放到原型管理中
		 */
		Splittable cell = new MuscleCells("肌肉甲", "肱二头肌");
		CellsManager.putCells("肌肉细胞", cell);
		cell.showSelf();
		/**
		 * 现在还需要一个肌肉细胞肌肉乙，除了名字，其他都一样，我们就使用克隆
		 */
		MuscleCells cell1 = (MuscleCells) CellsManager.getCells("肌肉细胞").clone();
		cell1.setCellName("肌肉乙");
		cell1.showSelf();
	}

}
```

---
#### 五、运行结果：

```
肌肉细胞 肌肉甲 肱二头肌
肌肉细胞 肌肉乙 肱二头肌
```