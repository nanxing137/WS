## 桥接模式
#### 桥接（Bridge）是用于把抽象化与实现化解耦，使得二者可以独立变化。这种类型的设计模式属于结构型模式，它通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。
#### 这种模式涉及到一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响。

---
### 介绍
**意图：** 将抽象部分与实现部分分离，使它们都可以独立的变化。    
**主要解决：** 在有多种可能会变化的情况下，用继承会造成类爆炸问题，扩展起来不灵活。    
**何时使用：** 实现系统可能有多个角度分类，每一种角度都可能变化。    
**如何解决：** 把这种多角度分类分离出来，让它们独立变化，减少它们之间耦合。  
**关键代码：** 抽象类依赖实现类。   
**优点：** 
1. 抽象和实现的分离。 
2. 优秀的扩展能力。 
3. 实现细节对客户透明。

**缺点：** 桥接模式的引入会增加系统的理解与设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设计与编程。

**使用场景：**  
1. 如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。
2. 对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。 
3. 一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。    

**注意事项：** 对于两个独立变化的维度，使用桥接模式再适合不过了。

---
### 实现：
##### 图书馆通过每个人借阅书籍编号、借阅人学号、日期生成借阅记录（BorrowingRecords）。借阅人有学生和老师，因为有三种因素，直接产生此类会产生类的指数爆炸。所以使用桥接器模式。
#### 一、首先定义Implementor接口，其中定义了其实现类必须要实现的各种输出接口。
**用户接口（UserAPI）：**
```
/**
 * @see 用户接口，定义输出不同用户行为
 * @author Thornhill
 *
 */
public interface UserAPI {
	public void printUser();
}
```

**日期接口（DateAPI）**
```
/**
 * @see 日期接口，定义输出日期的行为
 * @author Thornhill
 *
 */
public interface DateAPI {
	public void printDate();
}
```

**书籍接口（BookAPI）：**
```
/**
 * @see 书籍接口，定义输出书籍行为
 * @author Thornhill
 *
 */
public interface BookAPI {
	void printBookID();
}
```
#### 二、定义Implementor接口的实现类：
**学生实现（BorrowingStudents）：**
```
import implementor.user.UserAPI;

/**
 * @see 借书者的学生具体实现类（体现多态）
 * @author Thornhill
 *
 */
public class BorrowingStudents implements UserAPI {

	private String studentID;

	public BorrowingStudents(String studentID) {
		this.studentID = studentID;
	}

	@Override
	public void printUser() {
		System.out.print("学生:" + studentID);
	}

}
```
**老师实现（BorrowingTeacher）**
```
import implementor.user.UserAPI;

/**
 * @see 借书者的老师具体实现类（体现多态）
 * @author Thornhill
 *
 */
public class BorrowingTeacher implements UserAPI {

	private String teacherID;

	public BorrowingTeacher(String teacherID) {
		this.teacherID = teacherID;
	}

	@Override
	public void printUser() {
		System.out.print("老师:" + teacherID);
	}

}
```


**日期实现（DateAPI）**
```
/**
 * @see 日期接口，定义输出日期的行为
 * @author Thornhill
 *
 */
public interface DateAPI {
	public void printDate();
}
```

**书籍实现（BookAPI）：**
```
/**
 * @see 书籍接口，定义输出书籍行为
 * @author Thornhill
 *
 */
public interface BookAPI {
	void printBookID();
}
```
#### 三、定义桥接类Abstraction，其中有对多种Implementor接口的引用：
**抽象桥接类（BorrowingRecords）：**
```
import implementor.book.BookAPI;
import implementor.date.DateAPI;
import implementor.user.UserAPI;
/**
 * @see 抽象桥接类
 * @author Thornhill
 *
 */
public abstract class BorrowingRecords {
	protected BookAPI bookAPI;
	protected DateAPI dateAPI;
	protected UserAPI userAPI;

	abstract public void printRecords();
}

```
#### 四、创建BorrowingRecords类的子类ConcreteBorrowingRecord：
**实体桥接实现类（ConcreteBorrowingRecord）：**
```
import abstraction.BorrowingRecords;
import implementor.book.BookAPI;
import implementor.date.DateAPI;
import implementor.user.UserAPI;

/**
 * @see 实体桥接实现类
 * @author Thornhill
 *
 */
public class ConcreteBorrowingRecord extends BorrowingRecords {

	public ConcreteBorrowingRecord(UserAPI userAPI, DateAPI dateAPI, BookAPI bookAPI) {
		this.userAPI = userAPI;
		this.dateAPI = dateAPI;
		this.bookAPI = bookAPI;
	}

	@Override
	public void printRecords() {
		super.userAPI.printUser();
		System.out.print("在");
		super.dateAPI.printDate();
		System.out.print("借了编号为:");
		super.bookAPI.printBookID();
		System.out.println("的书");
	}

}
```
#### 五、使用不同的user，date，book创建不同的借阅记录：  
**桥接实例（BridgePatternDemo）：**
```
import abstraction.BorrowingRecords;
import concreteImplementor.ConcreteBorrowingRecord;
import refinedAbstraction.book.BorrowedBook;
import refinedAbstraction.date.BorrowingDate;
import refinedAbstraction.user.BorrowingStudents;
import refinedAbstraction.user.BorrowingTeacher;

/**
 * @see 桥接类客户端，测试输出
 * @author Thornhill
 *
 */
public class BridgePatternDemo {

	public static void main(String[] args) {

		BorrowingRecords borrowingRecords1 = new ConcreteBorrowingRecord(new BorrowingStudents("65535"),
				new BorrowingDate("2018年3月17日"), new BorrowedBook("1024"));
		borrowingRecords1.printRecords();
		BorrowingRecords borrowingRecords2 = new ConcreteBorrowingRecord(new BorrowingTeacher("65536"),
				new BorrowingDate("2018年3月18日"), new BorrowedBook("1025"));
		borrowingRecords2.printRecords();

	}

}
```
#### 六、验证输出：

```
学生:65535在2018年3月17日借了编号为:1024的书
老师:65536在2018年3月18日借了编号为:1025的书
```

---

### 结语：
**桥接器模式在系统有多角度分类时避免类型爆炸时使用。并且符合开闭原则，后期添加时只需要实现接口，即可使用（又体现多态）。**


[GitHub源代码](https://github.com/thornshell/Code)
