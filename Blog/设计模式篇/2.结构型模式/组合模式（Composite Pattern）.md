## 组合模式
#### 组合模式（Composite Pattern），又叫部分整体模式，是用于把一组相似的对象当作一个单一的对象。组合模式依据树形结构来组合对象，用来表示部分以及整体层次。这种类型的设计模式属于结构型模式，它创建了对象组的树形结构。
#### 这种模式创建了一个包含自己对象组的类。该类提供了修改相同对象组的方式。
#### 我们通过下面的实例来演示组合模式的用法。实例演示了一个电脑中的文件的层次结构。

---

#### 介绍： 
**意图：** 将对象组合成树形结构以表示"部分-整体"的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。  
**主要解决：** 它在我们树型结构的问题中，模糊了简单元素和复杂元素的概念，客户程序可以向处理简单元素一样来处理复杂元素，从而使得客户程序与复杂元素的内部结构解耦。    
**何时使用：** 1、您想表示对象的部分-整体层次结构（树形结构）。 2、您希望用户忽略组合对象与单个对象的不同，用户将统一地使用组合结构中的所有对象。    
**如何解决：** 树枝和叶子实现统一接口，树枝内部组合该接口。  
**关键代码：树枝内部组合该接口，并且含有内部属性 List，里面放 Component。**   
**应用实例：** 1、算术表达式包括操作数、操作符和另一个操作数，其中，另一个操作符也可以是操作树、操作符和另一个操作数。 2、在 JAVA AWT 和 SWING 中，对于 Button 和 Checkbox 是树叶，Container 是树枝。  

**分类：**
1. 安全组合模式：安全组合模式中，在抽象构件File中没有声明任何用于管理成员对象的方法，而是在具体类中声明并实现这些方法。
2. 透明组合模式：透明组合模式中，抽象构件File中声明了所有用于管理成员对象的方法，这样做的好处是确保所有的构件类都有相同的接口。  

**优点：** 
1. 高层模块调用简单。 
2. 节点自由增加。

**缺点：** 在使用组合模式中的安全组合模式时，其叶子和树枝的声明都是实现类，而不是接口，违反了依赖倒置原则。    
**使用场景：** 部分、整体场景，如树形菜单，文件、文件夹的管理。  
**注意事项：定义时为具体类。**

---

## 实现：   
**Linux和Unix的思想一切皆文件。那么，我们就使用组合模式表示其中的文件的树型结构。**  
**实现这里我们使用更加符合抽象编程的透明模式**  

#### 步骤一：
**创建各种文件的抽象父类File：**

```
/**
 * @see 透明组合模式，定义文件系统每个组件的所有功能， 并且提供了默认的无服务的方法
 * @author Thornhill
 *
 */
public abstract class File {
	protected String name;

	public void showSelf(int i) {
	// 根据递归深度调整输出格式
		while (i > 0) {
			System.out.print("	");
			i--;
		}
	}

	public void add(File f) {
		System.out.println("该文件不支持添加节点操作");
	}

	public void remove(File f) {
		System.out.println("该文件不支持移除节点操作");
	}

	public List<File> getChild() {
		System.out.println("该节点不支持获取子节点操作");
		return null;
	}
}
```
#### 步骤二：
**创建各种具体实现的文件：**
1. Image：

```
/**
 * @see 图片文件，不真正提供增，删，遍历操作，使用父类默认方法即可。
 * @author Thornhill
 *
 */
public class Image extends File {
	public Image(String name) {
		this.name = name;
	}

	@Override
	public void showSelf(int i) {
		super.showSelf(i);
		System.out.println("图片文件:" + name);

	}

}
```
2. Txt：

```
/**
 * @see 文本文件，不真正提供增，删，遍历操作，使用父类默认方法即可。
 * @author Thornhill
 *
 */
public class Txt extends File {
	public Txt(String name) {
		this.name = name;
	}

	@Override
	public void showSelf(int i) {
		super.showSelf(i);
		System.out.println("文本文件:" + name);
	}

}
```
3. Folder：

```
/**
 * @see 真正意义上实现增，删，遍历的文件夹文件， 唯一可以包含其他文件的文件
 * @author Thornhill
 *
 */
public class Folder extends File {
	private List<File> list = new ArrayList<>();

	public Folder(String name) {
		this.name = name;
	}

	@Override
	public void showSelf(int i) {
		super.showSelf(i);
		System.out.println("文件夹：" + name);
		for (File file : list) {
			file.showSelf(i + 1);
		}
	}

	@Override
	public void add(File f) {
		list.add(f);
	}

	@Override
	public void remove(File f) {
		list.remove(f);
	}

	@Override
	public List<File> getChild() {
		return list;
	}

}
```
#### 步骤三：
**编写测试**
```
public class CompositePatternDemo {

	public static void main(String[] args) {
		// 创建Linux文件目录第一级
		File root = new Folder("root");
		File users = new Folder("user");
		File bin = new Folder("bin");
		// 创建user目录下用户目录
		File user1 = new Folder("user1");
		File user2 = new Folder("user2");
		// 创建一些图片文件
		File image1 = new Image("image1");
		File image2 = new Image("image2");
		// 创建一些文本文件
		File txt1 = new Txt("txt1");
		File txt2 = new Txt("txt2");

		root.add(users);
		root.add(bin);

		users.add(user1);
		users.add(user2);

		bin.add(txt1);

		user1.add(image1);
		user1.add(txt2);

		user2.add(image2);

		root.showSelf(0);
	}

}
```
#### 步骤四：
**验证输出**
```
文件夹：root
	文件夹：user
		文件夹：user1
			图片文件:image1
			文本文件:txt2
		文件夹：user2
			图片文件:image2
	文件夹：bin
		文本文件:txt1

```

---
### 结语：
**组合模式在有明显结构层次的情境中，或者统一的使用一类对象时使用。**


[GitHub源代码](https://github.com/thornshell/Code)

