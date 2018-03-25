## 外观模式

**外观模式（Facade Pattern）隐藏系统的复杂性，并向客户端提供了可以访问系统的接口。这种类型的设计模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性。**

**这种模式涉及到一个单一的类，该类提供了客户端请求的简化方法和对现有系统类方法的委托调用。**

---

#### 介绍

**意图：**为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

**主要解决：**降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口。

**何时使用：** 1、客户端不需要知道系统内部的复杂联系，整个系统只需提供一个"接待员"即可。 2、定义系统的入口。

**如何解决：**客户端不与系统耦合，外观类与系统耦合。

**关键代码：**在客户端和复杂系统之间再加一层，这一层将调用顺序、依赖关系等处理好。

**应用实例：** 1、去医院看病，可能要去挂号、门诊、划价、取药，让患者或患者家属觉得很复杂，如果有提供接待人员，只让接待人员来处理，就很方便。 2、JAVA 的三层开发模式。

**优点：** 

1. 减少系统相互依赖。 
2.  提高灵活性。
3. 提高了安全性。

**缺点：**不符合开闭原则，如果要改东西很麻烦，继承重写都不合适。

**使用场景：**

1. 为复杂的模块或子系统提供外界访问的模块。 
2.  子系统相对独立。
3. 预防低水平人员带来的风险。

**注意事项：**在层次化结构中，可以使用外观模式定义系统中每一层的入口。

---

### 实现

**我们以厨师为例。我们将想要吃的食物告诉厨师，即调用厨师对象的做菜接口，由厨师做好饭，即厨师屏蔽了做菜 （底层）的实现。**



#### 步骤一

**创建关于做菜一系列子功能：**

1. 买菜（Groceries）：

   ```java
   /**
    * @see 做饭第一步，根据目标买菜
    * @author Thornhill
    *
    */
   public class Groceries {
   	private List<String> names;

   	public Groceries(List<String> names) {
   		this.names = names;
   	}

   	public void buy() {
   		System.out.println("买了" + names + "菜");
   	}
   }
   ```

   2. 洗菜（Clean）：

      ```java
      /**
       * @see 做菜第二步，清洗菜品
       * @author Thornhill
       *
       */
      public class Clean {
      	public void clean(List<String> names) {
      		System.out.println("洗" + names + "菜");
      	}
      }
      ```

      3. 烹饪（Cooking）:

         ```java
         /**
          * @see 做菜第三步，烹饪出锅
          * @author Thornhill
          *
          */
         public class Cooking {
         	public void cooking(List<String> names, String foodName) {
         		System.out.println("烹饪" + names + "食材，" + "产出" + foodName);
         	}
         }
         ```



#### 步骤二

**创建门面对象，即厨师（Chef）：**

```java
/**
 * @see 告诉厨师，将屏蔽底层实现，厨师知道买什么菜，怎么做饭。
 * @author Thornhill
 *
 */
public class Chef {
	public void cooking(String foodName) {
		List<String> names = new ArrayList<>();
		switch (foodName) {
		case "西红柿鸡蛋":
			names.add("鸡蛋");
			names.add("西红柿");

			break;
		case "京酱肉丝":
			names.add("肉");
			names.add("葱");
			break;
		default:
			System.out.println("您的厨师暂时不支持此功能");
			return;
		// break;
		}
		Groceries groceries = new Groceries(names);
		// 完成购买
		groceries.buy();

		Clean clean = new Clean();
		// 完成清洗
		clean.clean(names);

		Cooking cooking = new Cooking();
		// 完成烹饪
		cooking.cooking(names, foodName);
	}
}
```

#### 步骤三

**创建用户（User）使用：**

```java
/**
 * @see 任性的用户只需要提出需求使用即可，不需要关注具体的实现细节
 * @author Thornhill
 *
 */
public class User {

	public static void main(String[] args) {
		Chef chef = new Chef();
		// 把想吃的菜告诉厨师
		chef.cooking("西红柿鸡蛋");
		chef.cooking("京酱肉丝");
		chef.cooking("满汉全席");
	}

}
```



#### 步骤四

**测试输出：**

```java
买了[鸡蛋, 西红柿]菜
洗[鸡蛋, 西红柿]菜
烹饪[鸡蛋, 西红柿]食材，产出西红柿鸡蛋
买了[肉, 葱]菜
洗[肉, 葱]菜
烹饪[肉, 葱]食材，产出京酱肉丝
您的厨师暂时不支持此功能
```

---

### 结语：
**外观模式的应用非常广泛，从系统的轮子到用户的需求，都是屏蔽了细节，只提供UI。不过要小心使用，因为这将极大程度违背了开闭原则，对将来的修改造成了困扰。**


[GitHub源代码](https://github.com/thornshell/Code)