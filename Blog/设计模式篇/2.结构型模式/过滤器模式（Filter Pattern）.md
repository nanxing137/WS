## 过滤器模式（Filter Pattern）
**过滤器模式（Filter Pattern）或标准模式（Criteria Pattern）是一种设计模式，这种模式允许开发人员使用不同的标准来过滤一组对象，通过逻辑运算以解耦的方式把它们连接起来。这种类型的设计模式属于结构型模式，它结合多个标准来获得单一标准。**

---
#### 实现
我们将创建一个 Person 对象、Criteria 接口和实现了该接口的实体类，来过滤 Person 对象的列表。CriteriaPatternDemo，我们的演示类使用 Criteria 对象，基于各种标准和它们的结合来过滤 Person 对象的列表。
#### 步骤 1：
1. 创建一个枚举（PersonAttributes），保存人（Person）的各种属性
> Java字符使用Unicode编码，可以使用中文，但不建议使用。这里为了方便输出随意为之。
```
/**
 * @see Person类中可选的属性
 * @author Thornhill
 *
 */
public enum PersonAttributes {
	男, 女, 医生, 程序员;
	private PersonAttributes() {
	}

	public static PersonAttributes getMale() {
		return 男;
	}

	public static PersonAttributes getFemale() {
		return 女;
	}

	public static PersonAttributes getProgrammer() {
		return 程序员;
	}

	public static PersonAttributes getDoctor() {
		return PersonAttributes.医生;
	}

}
```
2. 创建一个类（Person），在该类上应用标准。（即各种过滤）

```
/**
 * @see 被各种过滤器过滤的实体
 * @author Thornhill
 *
 */
public class Person {
	private PersonAttributes gender;
	private PersonAttributes career;
	private String name;

	public Person(String name, PersonAttributes gender, PersonAttributes career) {
		this.name = name;
		this.gender = gender;
		this.career = career;
	}

	/**
	 * @see 重写toString，便于直接输出
	 */
	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder();
		sb.append(name + " " + gender + " " + career);
		return sb.toString();
		// return super.toString();
	}

	public PersonAttributes getGender() {
		return gender;
	}

	public PersonAttributes getCareer() {
		return career;
	}

	public String getName() {
		return name;
	}
}
```
#### 步骤 2：
为标准（Criteria）创建一个接口（Criteria）。

```
/**
 * @see 定义过滤的行为
 * @author Thornhill
 *
 */
public interface Criteria {
	public List<Person> meetCriteria(List<Person> persons);
}
```
#### 步骤 3：
创建实现了 Criteria 接口的各种过滤器实体类。
1. 找出医生：
```
/**
 * @see 找出是医生的实体
 * @author Thornhill
 *
 */
public class CriteriaDoctor implements Criteria {

	@Override
	public List<Person> meetCriteria(List<Person> persons) {
		List<Person> list = new ArrayList<Person>();
		for (Person person : persons) {
			if (person.getCareer().equals(PersonAttributes.医生)) {
				list.add(person);
			}
		}
		return list;
	}

}
```
2. 找出程序员：

```
/**
 * @see 找出是程序员的实体
 * @author Thornhill
 *
 */
public class CriteriaProgrammer implements Criteria {

	@Override
	public List<Person> meetCriteria(List<Person> persons) {
		List<Person> list = new ArrayList<Person>();
		for (Person person : persons) {
			if (person.getCareer().equals(PersonAttributes.程序员)) {
				list.add(person);
			}
		}
		return list;
	}

}
```
3. 找出男人：

```
/**
 * @see 找出是男的实体
 * @author Thornhill
 *
 */
public class CriteriaMale implements Criteria {

	@Override
	public List<Person> meetCriteria(List<Person> persons) {
		List<Person> list = new ArrayList<Person>();
		for (Person person : persons) {
			if (person.getGender().equals(PersonAttributes.男)) {
				list.add(person);
			}
		}
		return list;
	}

}
```
4. 找出女人：
```
/**
 * @see 找出是女人的实体
 * @author Thornhill
 *
 */
public class CriteriaFemale implements Criteria {

	@Override
	public List<Person> meetCriteria(List<Person> persons) {
		List<Person> list = new ArrayList<Person>();
		for (Person person : persons) {
			if (person.getGender().equals(PersonAttributes.女)) {
				list.add(person);
			}
		}
		return list;
	}

}
```
5. 混合查询：
    1. 与查询：查询输入查询条件的交集
    ```
    /**
     * @see 查询所有满足输入条件的交集
     * @author Thornhill
     *
     */
    public class FilterAnd implements Criteria {
    
    	private List<Criteria> list = new ArrayList<Criteria>();
    
    	public FilterAnd(Criteria... criterias) {
    		for (Criteria criteria : criterias) {
    			list.add(criteria);
    		}
    	}
    
    	@Override
    	public List<Person> meetCriteria(List<Person> persons) {
    		List<Person> result = new ArrayList<Person>(persons);
    		for (Criteria criteria : list) {
    			result = criteria.meetCriteria(result);
    		}
    		return result;
    	}
    
    }
    ```
    2. 或查询：返回所有满足查询条件的并集
    >     因为并集需要去重，所以这里直接使用Set
    
    ```
    /**
     * @see 查询所有输入条件的并集
     * @author Thornhill
     *
     */
    public class FilterOr implements Criteria {
    	private List<Criteria> list = new ArrayList<Criteria>();
    
    	public FilterOr(Criteria... criterias) {
    		for (Criteria criteria : criterias) {
    			list.add(criteria);
    		}
    	}
    
    	@Override
    	public List<Person> meetCriteria(List<Person> persons) {
    		Set<Person> result = new HashSet<Person>();
    		for (Criteria criteria : list) {
    			result.addAll(criteria.meetCriteria(persons));
    		}
    		return new ArrayList<Person>(result);
    	}
    
    }
    ```

#### 步骤4：
使用不同的标准（Criteria）和它们的结合来过滤 Person 对象的列表。

```
public class CriteriaPatternDemo {

	public static void main(String[] args) {
		List<Person> persons = new ArrayList<Person>();
		//加入元素
		persons.add(new Person("张1", PersonAttributes.男, PersonAttributes.程序员));
		persons.add(new Person("张2", PersonAttributes.男, PersonAttributes.医生));
		persons.add(new Person("张3", PersonAttributes.女, PersonAttributes.程序员));
		persons.add(new Person("张4", PersonAttributes.女, PersonAttributes.医生));
		//创建筛选
		Criteria criteriaMale = new CriteriaMale();
		Criteria criteriaFemale = new CriteriaFemale();
		Criteria criteriaDoctor = new CriteriaDoctor();
		Criteria criteriaProgrammer = new CriteriaProgrammer();
		//创建混合筛选
		Criteria filterAnd = new FilterAnd(criteriaFemale,criteriaProgrammer);//查询女程序员
		Criteria filterOr = new FilterOr(criteriaDoctor,criteriaMale);//查询医生或男
		//输出
		System.out.println(persons.toString());
		System.out.println(criteriaMale.meetCriteria(persons));
		System.out.println(criteriaFemale.meetCriteria(persons));
		System.out.println(criteriaProgrammer.meetCriteria(persons));
		System.out.println(criteriaDoctor.meetCriteria(persons));
		System.out.println("------------混合查询------------");
		System.out.println(filterAnd.meetCriteria(persons));
		System.out.println(filterOr.meetCriteria(persons));
		
	}

}
```
#### 步骤 5：
验证输出。

```
[张1 男 程序员, 张2 男 医生, 张3 女 程序员, 张4 女 医生]
[张1 男 程序员, 张2 男 医生]
[张3 女 程序员, 张4 女 医生]
[张1 男 程序员, 张3 女 程序员]
[张2 男 医生, 张4 女 医生]
------------混合查询------------
[张3 女 程序员]
[张2 男 医生, 张1 男 程序员, 张4 女 医生]
```
---

### 结语：
**过滤器模式优秀在过滤条件可以自动组和，上一次的结果可以用于下一次的输入。**


[GitHub源代码](https://github.com/thornshell/Code)