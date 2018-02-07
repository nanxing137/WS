# Java多线程的使用通信和控制
### 1. Java多线程的使用和通信

##### 实现多线程有两种方式
1. 继承Thread父类
2. 实现Runable接口
==的==
> 使用继承hread的方式：	

```
public class MyThread extends Thread{
	
	private String name;
	public Thread0(String name) {
		this.name = name;
	}
	@Override
	public void run() {
		// 具体功能
	}

}
```

> 实现Runable的方式：

```
public class MyRunable implements Runnable{

	@Override
	public void run() {
	    // 具体功能	
	}
}
```
2.开启线程   
    &nbsp;&nbsp;&nbsp;&nbsp;1.继承了Thread父类的具体对象可以直接调用start()方法，但直接用此方法，无法实现进程间数据通信。关于通信之后再说。也可以将此对象再次放到Thread中运行。  
    &nbsp;&nbsp;&nbsp;&nbsp;2.实现了Runable的类的具体对象没有start()方法。只能放到Thread中运行。


> 继承了Thread的运行：
```
MyThread myThread = new MyThread();
//可以直接把线程运行
myThread.start();
//或者再放到线程里运行
new Thread(myThread).start();
```
> 实现了Runable接口的运行：


```
MyRunable myRunable = new MyRunable();
Thread(myRunable).start();
```
那么问题来了，直接运行和放进Thread中有什么区别呢

**如果直接运行，那么多个进程之间不会共享变量。每一个变量线程都会有自己的变量，但如果只new一个对象，然后放到Thread中运行，那么多个线程之间会共享一个对象的变量**

**所以我认为：new几个对象，内存中就有几个对象的空间。第一步中，无论是继承Thread还是实现Runable，每一个new出来的对象就像一个具体的任务，而第二步放到Thread中时，就像把这个任务交给了new的线程中，而且一个任务可以交给多个Thread去处理！！！符合面向对象的思想！！！**
> 对于以上解释，正确的打开方式：

```
MyRunable myRunable = new MyRunable()
new Thread(myRunalbe).start();
new Thread(myRunalbe).start();
//这样就创建了两个线程，去完成一个myRunable的"任务"
//而且线程间共享同一个任务（myRunable）中的数据
```

### 2.Java中的同步与异步

#### 1. 异步
原理上讲，异步就是各干各的，互不干涉的。即使共享一个变量也不会互相礼让，那么即使最简单的自增操作，也会因为分三步而有可能将数据读脏。
#### 2. 同步
同步就是为了解决这个问题所产生的。用以下各种方法来实现线程同步。    

1. volatile： 这是一个类型修饰符，被他修饰的对象，编译器每次访问这个的对象，都会小心翼翼的从内存中取，而不是粗心的从寄存器中取。因为不同线程可能导致内存中数据已经更改而寄存器中数据没有更改。然而他并不能保证进程安全。因为有序性没有满足，只满足了可见性。要使volatile变量提供理想的线程安全,必须同时满足下面两个条件:  
    1.   对变量的写操作不依赖于当前值。
    2.   该变量没有包含在具有其他变量的不变式中    
    
        即只有在状态真正独立于程序内其他内容时才能使用volatile。
 
2.  synchronized： 一个线程互斥执行代码过程如下：

        1. 获得同步锁；
        2. 清空工作内存；
        3. 从主内存拷贝对象副本到工作内存；
        4. 执行代码(计算或者输出等)；
        5. 刷新主内存数据；
        6. 释放同步锁。
所以，synchronized既保证了多线程的并发有序性，又保证了多线程的内存可见性。

synchronized的使用方法有两种：
- 锁对象
- 锁方法
> 锁对象：

```
class myClass{
    public Integer myInt = 10;
    public void myMethod0(){
        synchronized(this) {
            //被锁住的代码区域
        }
    }
    
    public void myMethod1(){
        synchronized(myInt) {
            //被锁住的代码区域
        }
    }
}
```
第一个函数相当于将自身对象上锁，谁拿到this，谁就能执行被锁住的区域。第二个将对象myInt上锁。通常情况代码区域操作谁，就锁谁 。
> 锁方法

```
class myClass{
    void synchronized myMethod2(){
        //被锁住的代码区域
    }
}
```
将方法上锁，此方法同时只能被一个线程方法，而且在操作结束释放锁之前，其他线程无法执行该方法。    

注：    
1. 虽然可以使用synchronized来定义方法，但synchronized并不属于方法定义的一部分，因此，synchronized关键字不能被继承。
2. 在定义接口方法时不能使用synchronized关键字。
3. 构造方法不能使用synchronized关键字，但可以使用synchronized代码块来进行同步。 

总结：  
A. 无论synchronized关键字加在方法上还是对象上，如果它作用的对象是非静态的，则它取得的锁是对象；如果synchronized作用的对象是一个静态方法或一个类，则它取得的锁是对类，该类所有的对象同一把锁。    
B. 每个对象只有一个锁（lock）与之相关联，谁拿到这个锁谁就可以运行它所控制的那段代码。    
C. 实现同步是要很大的系统开销作为代价的，甚至可能造成死锁，所以尽量避免无谓的同步控制。
