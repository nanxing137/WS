## 工厂模式
#### 工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。
#### 在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

> 下面举一个栗子：

**我们都是人类（Human），抽象一个Humen接口：**

```
public interface Human {
	/**
	 * 输出身份
	 */
	public void showIdentity();
	/**
	 * 输出工作内容
	 */
	public void showWorkContent();
	/**
	 * 输出工资
	 */
	public void showSalary();
	/**
	 * 自我介绍
	 */
	public void showSelf();
}
```
**先创建一个程序员：**

```
public class Programmer implements Human {

	private Integer salary = 1024;

	public Integer getSalary() {
		return salary;
	}

	public void setSalary(Integer salary) {
		this.salary = salary;
	}

	@Override
	public void showIdentity() {
		System.out.println("我是程序员");
	}

	@Override
	public void showWorkContent() {
		System.out.println("996CODING，懂得自然懂");
	}

	@Override
	public void showSalary() {
		System.out.println("工资：" + salary);

	}

	@Override
	public void showSelf() {
		showIdentity();
		showWorkContent();
		showSalary();

	}

}

```
**一个学生：**

```
public class Student implements Human {

	private Integer salary = 0;

	public Integer getSalary() {
		return salary;
	}

	public void setSalary(Integer salary) {
		this.salary = salary;
	}

	@Override
	public void showIdentity() {
		System.out.println("我是学生");

	}

	@Override
	public void showWorkContent() {
		System.out.println("念书");

	}

	@Override
	public void showSalary() {
		System.out.println("工资NuN");

	}

	@Override
	public void showSelf() {
		showIdentity();
		showWorkContent();
		showSalary();

	}

}
```
**和一个老师：**

```
public class Teacher implements Human {

	private Integer salary = 1024;

	public Integer getSalary() {
		return salary;
	}

	public void setSalary(Integer salary) {
		this.salary = salary;
	}

	@Override
	public void showIdentity() {
		System.out.println("我是老师");

	}

	@Override
	public void showWorkContent() {
		System.out.println("教书即可");

	}

	@Override
	public void showSalary() {
		System.out.println("工资：" + salary);

	}

	@Override
	public void showSelf() {
		showIdentity();
		showWorkContent();
		showSalary();

	}

}
```
**还有最重要的初代造人工厂0.0（静态工厂）：**
```
public class HumenFactory0 {
	public static final Integer PROGRAMMER = 0;
	public static final Integer STUDENT = 1;
	public static final Integer TRACHER = 2;

	public static Human getHumen(Integer type) {
		Human human = null;
		switch (type) {
		case 0:
			human = new Programmer();
			break;
		case 1:
			human = new Student();
			break;
		case 2:
			human = new Teacher();
			break;
		default:
			break;
		}
		return human;
	}
}
```
**跑起来：**

```
public class Test {

	public static void main(String[] args) {
		Programmer programmer =  (Programmer) HumenFactory0.getHumen(HumenFactory0.PROGRAMMER);
		Student student =  (Student) HumenFactory0.getHumen(HumenFactory0.STUDENT);
		Teacher teacher =  (Teacher) HumenFactory0.getHumen(HumenFactory0.TRACHER);
		
		programmer.setSalary(2048);
		student.setSalary(2048);
		teacher.setSalary(2048);
		
		
		programmer.showSelf();
		student.showSelf();
		teacher.showSelf();

	}

}
```
**运行结果：（Perfect）**

```
我是程序员
996CODING，懂得自然懂
工资：2048
我是学生
念书
工资NuN
我是老师
教书即可
工资：2048
```
##### 优点：
1. 能够将创建过程打包（废话）
2. 实现简单
##### 缺点：
1. 静态方法，当添加新的成员时，既要加类，也要改现有工厂代码，不符合开闭原则。
2. 修改工厂代码不仅是在原有工厂类中修改，而且是在原有函数中修改，容易产生各种各样的错误。
3. 不同的产品需要不同额外参数的时候不支持。（使用僵硬）



>但是， 劳动人民的智慧是伟大的

**二代造人工厂1.0：**

```
public class HumanFactory1 {
	public static <T> T getClass(Class<? extends T> c) {
		T result = null;
		try {
			result = (T) Class.forName(c.getName()).newInstance();
		} catch (InstantiationException | IllegalAccessException | ClassNotFoundException e) {

			// TODO 自动生成的 catch 块
			e.printStackTrace();
		}
		return result;
	}
}
```
**跑**：

```
public class Test {

	public static void main(String[] args) {
		
		/*
		Programmer programmer =  (Programmer) HumenFactory0.getHumen(HumenFactory0.PROGRAMMER);
		Student student =  (Student) HumenFactory0.getHumen(HumenFactory0.STUDENT);
		Teacher teacher =  (Teacher) HumenFactory0.getHumen(HumenFactory0.TRACHER);
		
		programmer.setSalary(2048);
		student.setSalary(2048);
		teacher.setSalary(2048);
		
		
		programmer.showSelf();
		student.showSelf();
		teacher.showSelf();
		
		*/
		
		Programmer programmer1 =  HumanFactory1.getClass(Programmer.class);
		Student student1 =   HumanFactory1.getClass(Student.class);
		Teacher teacher1 =  HumanFactory1.getClass(Teacher.class);
		
		programmer1.setSalary(4096);
		student1.setSalary(4096);
		teacher1.setSalary(4096);
		
		
		programmer1.showSelf();
		student1.showSelf();
		teacher1.showSelf();
		
		
		
		

	}

}
```
结果：（Excellent）
```
我是程序员
996CODING，懂得自然懂
工资：4096
我是学生
念书
工资NuN
我是老师
教书即可
工资：4096
```
##### 优点：
1. 能够将创建过程打包（废话）。
2. 反射机制支持，只需要写一次，不出意外用到天荒地老。
3. ~~实现方法高大上，装逼必备。~~
##### 缺点：
1. 静态方法，当添加新的成员时，既要加类，也要改现有工厂代码，不符合开闭原则。
2. 构造是只能调用默认的无参构造函数。（更加僵硬）
3.Java的反射机制，效率奇低无比。（编译器无法提前优化代码导致） 
4. 实现复杂，比起初代工厂。

> 甚至还有一个版本：

**三代造人工厂2.0：**

```
public class HumanFactory2 {
	public static Programmer getProgrammer() {
		return new Programmer();
	}

	public static Student getStudent() {
		return new Student();
	}

	public static Teacher getTeacher() {
		return new Teacher();
	}
}

```
**Run：**

```
public class Test {

	public static void main(String[] args) {
		
		/*
		Programmer programmer =  (Programmer) HumenFactory0.getHumen(HumenFactory0.PROGRAMMER);
		Student student =  (Student) HumenFactory0.getHumen(HumenFactory0.STUDENT);
		Teacher teacher =  (Teacher) HumenFactory0.getHumen(HumenFactory0.TRACHER);
		
		programmer.setSalary(2048);
		student.setSalary(2048);
		teacher.setSalary(2048);
		
		
		programmer.showSelf();
		student.showSelf();
		teacher.showSelf();
		
		*/
		/*
		Programmer programmer1 =  HumanFactory1.getClass(Programmer.class);
		Student student1 =   HumanFactory1.getClass(Student.class);
		Teacher teacher1 =  HumanFactory1.getClass(Teacher.class);
		
		programmer1.setSalary(4096);
		student1.setSalary(4096);
		teacher1.setSalary(4096);
		
		
		programmer1.showSelf();
		student1.showSelf();
		teacher1.showSelf();
		
		*/
		Programmer programmer2 =  HumanFactory2.getProgrammer();
		Student student2 =   HumanFactory2.getStudent();
		Teacher teacher2 =  HumanFactory2.getTeacher();
		
		programmer2.setSalary(8192);
		student2.setSalary(8192);
		teacher2.setSalary(8192);
		
		
		programmer2.showSelf();
		student2.showSelf();
		teacher2.showSelf();
		
		
		
		

	}

}
```

**运行结果：（Amazing）**

```
我是程序员
996CODING，懂得自然懂
工资：8192
我是学生
念书
工资NuN
我是老师
教书即可
工资：8192
```
这种工厂和初代工厂很像，所以优缺点也差不多
##### 优点：
1. 能够将创建过程打包（废话）。
2. 比起初代，更符合开闭原则，虽然略逊于二代。新增对象不用更改原函数，只需增加啊新函数即可，但是要在已有工厂中修改。
3. 实现最简单。
##### 缺点：
1. 不同的产品需要不同额外参数的时候不支持。（同初代）
2. 有对象时，需要在原有工厂中修改。

[GitHub源代码](https://github.com/thornshell/WS)