## 装饰器模式
**装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。    
这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。**

---
#### 介绍
**意图：** 动态地给一个对象添加一些额外的职责。就增加功能来说，装饰器模式相比生成子类更为灵活。  
**主要解决：** 一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。    
**何时使用：在不想增加很多子类的情况下扩展类。**    
**如何解决：** 将具体功能职责划分，同时继承装饰者模式。  
**关键代码：**  
1. Component 类充当抽象角色，不应该具体实现。
2. 修饰类引用和继承 Component 类，具体扩展类重写父类方法。  

**优点：** 装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。  
**缺点：** 多层装饰比较复杂。（无法直观的了解一个被装饰类的全貌）      
**使用场景：** 
1. 扩展一个类的功能。 
2. 动态增加功能，动态撤销。 

**装饰模式和桥接模式的区别：**  两个模式都是为了解决过多子类对象问题。但他们的诱因不一样。桥接模式是对象自身现有机制沿着多个维度变化，是既有部分不稳定。装饰模式是为了增加新的功能。    
**注意事项：可代替继承。**

---
## 实现
> 学习就是一种装饰器模式（假设学到的知识不会忘记），我们不断的学习就是不断的给被装饰类加上新的装饰器。

#### 步骤一：
> 创建学习接口（Learn）：

```
/**
 * @see 具体学习的技能（这里作用为输出自己学到的技能）
 * @author Thornhill
 *
 */
public interface Learn {
	public void learnSkills();
}
```
#### 步骤二：
> 创建具体实现学习接口的学生（Student）：

```
/**
 * @see 实现了学习接口的学生,这里是被修饰者的角色
 * @author Thornhill
 *
 */
public class Student implements Learn {

	private String name;

	public Student(String name) {
		this.name = name;
	}

	@Override
	public void learnSkills() {
		System.out.println(name + "学会了技能:");
	}

}
```
#### 步骤三：
> 创建装饰角色（Skills）：

```
/**
 * @see 装饰者的基类，持有一个学习者对象，被装饰时共享一个学习对象
 * @author Thornhill
 *
 */
public class Skills implements Learn {
	private Learn learner;

	public Skills(Learn learner) {
		this.learner = learner;
	}

	@Override
	public void learnSkills() {
		if (learner != null) {
			learner.learnSkills();
		}

	}

}
```
#### 步骤四：
> 创建实际修饰角色（CSkills）：

```
/**
 * @see 具体修饰者角色，最高层继承于修饰者（Sills），为了给同一个方法灵活叠加新的方法，多层继承于自己。
 * @author Thornhill
 *
 */
public class CSkills extends Skills {

	private String skillName;

	public CSkills(Learn learner, String skillName) {
		super(learner);
		this.skillName = skillName;
	}

	@Override
	public void learnSkills() {
		super.learnSkills();
		System.out.println("	" + skillName);
	}

}
```
#### 步骤五：
> 测试功能：

```
public class DecoratorPatternDemo {

	public static void main(String[] args) {
		// 创建学生“小明”
		Learn xiaoming = new Student("小明");
		// 创建修饰者“skills”
		Skills skills = new Skills(xiaoming);
		// 多层修饰skill
		// 即模拟小明多次学习新技能
		skills = new CSkills(skills, "Java");
		skills = new CSkills(skills, "Python");
		skills = new CSkills(skills, "Hadoop");
		// 输出小明的技能
		skills.learnSkills();

	}

}
```
#### 步骤六：

> 测试输出：

```
小明学会了技能:
	Java
	Python
	Hadoop
```

---

### 结语：
**个人认为，装饰模式即不适用继承的方式，通过装饰者和实际装饰者的多次自身继承，达到多次重写（修饰）需要修饰的方法的目的。**


[GitHub源代码](https://github.com/thornshell/Code)