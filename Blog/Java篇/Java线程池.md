## Java线程池

---

### 一、Executor（执行器）框架

​	**创建一个新线程是有一定代价的，以为涉及与操作系统的交互。如果程序中创建了大量的生命周期很短的线程，应该使用线程池（thread pool）。一个线程池中包含许多准备运行的空闲线程。将Runable对象交给线程池，就会有一个线程调用run方法。当run方法退出时，线程不会死亡，而是在池中准备为下一个请求提供服务。**

​	**另一个使用线程池的理由是减少并发线程的数目。创建大量的线程会大大降低性能甚至使虚拟机崩溃。如果有一个会创建许多线程的算法，应该使用一个线程数“固定的”线程池以限制并发线程的总数。**

​	**执行器（Executor）类有许多静态工厂方法用来构建线程池，下表中对这些方法进行了汇总。**

| 方法                             | 描述                                                         |
| :------------------------------- | :----------------------------------------------------------- |
| newCachedThreadPool              | 必要时创建新线程；空闲线程会被保持60秒                       |
| newFixedThreadPool               | 该池包含固定数量的线程；空闲线程会一直被保留                 |
| newSingleThreadExecutor          | 只有一个线程的“池”，该线程顺序执行每一个提交的任务（类似于Swing事件分配线程） |
| newScheduledThreadPool           | 用于预定执行而构建的固定线程池，替代java.util.Timer          |
| newSingleThreadScheduledExecutor | 用于预定执行而构建的单线程“池”                               |



****

#### 线程池：

​	**newCachedThreadPoll**

> newCachedThreadPool方法构建了一个线程池，对于每个任务，如果有空闲线程池可用，立即让它执行任务，如果没有可用的空闲线程，则会创建一个新线程。
>
> - 缓存型池子，先查看池中有没有以前建立的线程，如果有，就 reuse 如果没有，就建一个新的线程加入池中
>
> - 缓存型池子通常用于执行一些生存期很短的异步型任务 因此在一些面向连接的 daemon 型 SERVER 中用得不多。但对于生存期短的异步任务，它是 Executor 的首选。
>
> - 能 reuse 的线程，必须是 timeout IDLE 内的池中线程，缺省 timeout 是 60s,超过这个 IDLE 时长，线程实例将被终止及移出池。
>
>   > 注意，放入 CachedThreadPool 的线程不必担心其结束，超过 TIMEOUT 不活动，其会自动被终止。

​	**newFixedThreadPool**

> newFixedThreadPool方法构建一个具有固定大小的线程池。如果提交的任务数多余空闲的进程数，那么把得不到服务的任务放置到队列中。当其他任务完成以后再运行他们。

从方法的源代码看，cache池和fixed 池调用的是同一个底层 池，只不过参数不同:

- fixed 池线程数固定，并且是0秒IDLE（无IDLE）。

- cache 池线程数支持 0-Integer.MAX_VALUE(显然完全没考虑主机的资源承受能力），60 秒 IDLE 。

  **newSingleThreadExecutor**

> newSingleThreadExecutor是一个退化了的大小为 1 的线程池：由一个线程执行提交的任务，一个接一个。
>
> - 单例线程，任意时间池中只能有一个线程
> - 用的是和 cache 池和 fixed 池相同的底层池，但线程数目是 1-1,0 秒 IDLE（无 IDLE）

一般来说，CachedTheadPool 在程序执行过程中通常会创建与所需数量相同的线程，然后在它回收旧线程时停止创建新线程，因此它是合理的 Executor 的首选，只有当这种方式会引发问题时（比如需要大量长时间面向连接的线程时），才需要考虑用 FixedThreadPool。（该段话摘自《Thinking in Java》第四版）

​	这三个方法返回实现了ExecutorService接口的ThreadPoolExecutor类的对象。

​	可用下面的方法之一将一个Runnable对象或Callable对象提交给ExecutorService：

```java
Future<?> submit(Runnable task)
Future<T> submit(Runnalbe task, T result)
Future<T> submit(Callable<T> task)
```

​	该池会在方便的时候尽早执行提交的任务。调用submit时，会得到一个Future对象，可用来查询该任务的状态。

​	第一个submit方法返回一个奇怪样子的Future<?>。可以使用这样一个对象来调用isDone、cancel或isCancelled。但是，get方法在完成的时候只是简单的返回null。

​	第二个版本的submit也提交一个Runnable，并且Future的get方法在完成的时候返回指定的result对象。

​	第三个版本的submit提交一个Callable，并且返回的Future对象将在计算结果准备好的时候得到它。

​	

​	当用完一个线程池的时候，调用shutdown。该方法启动该池的关闭序列。被关闭的执行器不再接受新的任务。当所有任务都完成以后，线程池中的线程死亡。另一种方法时调用shutdownNow。该池取消尚未开始的所有任务并试图中断正在运行的线程。

​	下面总结了在使用连接池时应该做的事：

​	1）调用Executors类中的静态方法newCachedThreadPool或newFixedThreadPool。

​	2）调用submit提交Runable或Callable对象。

​	3）如果想要取消一个任务，或如果提交C  allable对象，那就要保存好返回的Future对象。

​	4）当不再提交任何任务时，调用shutdown。

---

#### 应用程序编程接口（API）

> **API	java.util.concurrent.Executors	5.0**

- ExecutorService newCachedThreadPool()

  返回一个带缓存的线程池，该池在必要的时候创建线程，在线程空闲60秒之后终止线程。

- ExecutorService newFixedThreadPool(int threads)

  返回一个线程池，该池中的线程数由参数指定。

- ExecutorService newSingleThreadExecutor()

  返回一个执行器，她在一个单线程中依次执行各个任务。

	 **API	java.util.concurrent.ExecutorService	5.0**

- Future<T> submit(Callable<T> task)

- Future<T> submit(Runnable task, T resutl)

- Future<?> submit(Runnable task)

  提交指定的任务去执行。

- void shutdown()

  关闭服务，会先完成已经提交的任务而不再接收新的任务。

	 **API	java.util.concurrent.ThreadPoolExecutor	5.0**

- int getLargestPoolSize()

  返回线程池在该执行器生命周期中的最大尺寸。

---

## 自定义线程池

#### 一、Java中的ThreadPoolExecutor类

​	java.uitl.concurrent.ThreadPoolExecutor类是线程池中最核心的一个类，因此如果要透彻地了解Java中的线程池，必须先了解这个类。下面我们来看一下ThreadPoolExecutor类的具体实现源码。

​	在ThreadPoolExecutor类中提供了四个构造方法：

```java
public class ThreadPoolExecutor extends AbstractExecutorService {
    .....
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit, BlockingQueue<Runnable> workQueue);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit, BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit, BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit, BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler);
    ...
}
```

从上面的代码可以得知，ThreadPoolExecutor继承了AbstractExecutorService类，并提供了四个构造器，事实上，通过观察每个构造器的源码具体实现，发现前面三个构造器都是调用的第四个构造器进行的初始化工作。

​	下面解释下一下构造器中各个参数的含义：

- corePoolSize：核心池的大小，这个参数跟后面讲述的线程池的实现原理有非常大的关系。在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；
- maximumPoolSize：线程池最大线程数，这个参数也是一个非常重要的参数，它表示在线程池中最多能创建多少个线程；
- keepAliveTime：表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize，即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize。但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；
- unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性：

```java
TimeUnit.DAYS;              //天
TimeUnit.HOURS;             //小时
TimeUnit.MINUTES;           //分钟
TimeUnit.SECONDS;           //秒
TimeUnit.MILLISECONDS;      //毫秒
TimeUnit.MICROSECONDS;      //微妙
TimeUnit.NANOSECONDS;       //纳秒
```

- workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：

```java
SynchronousQueue;
ArrayBlockingQueue;
LinkedBlockingQueue;
```

| 队列                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| SynchronousQueue;    | 直接提交。缓冲队列采用 SynchronousQueue，它将任务直接交给线程处理而不保持它们。如果不存在可用于立即运行任务的线程（即线程池中的线程都在工作），则试图把任务加入缓冲队列将会失败，因此会构造一个新的线程来处理新添加的任务，并将其加入到线程池中。直接提交通常要求无界maximumPoolSizes（Integer.MAX_VALUE） 以避免拒绝新提交的任务。newCachedThreadPool 采用的便是这种策略。 |
| ArrayBlockingQueue;  | 有界队列。当使用有限的 maximumPoolSizes 时，有界队列（一般缓冲队列使用 ArrayBlockingQueue，并制定队列的最大长度）有助于防止资源耗尽，但是可能较难调整和控制，队列大小和最大池大小需要相互折衷，需要设定合理的参数。 |
| LinkedBlockingQueue; | 无界队列。使用无界队列（典型的便是采用预定义容量的 LinkedBlockingQueue，理论上是该缓冲队列可以对无限多的任务排队）将导致在所有 corePoolSize 线程都工作的情况下将新任务加入到缓冲队列中。这样，创建的线程就不会超过 corePoolSize，也因此，maximumPoolSize 的值也就无效了。当每个任务完全独立于其他任务，即任务执行互不影响时，适合于使用无界队列。newFixedThreadPool采用的便是这种策略。 |

- threadFactory：线程工厂，主要用来创建线程；
- handler：表示当拒绝处理任务时的策略，有以下四种取值：

```java
ThreadPoolExecutor.AbortPolicy			//丢弃任务并抛出RejectedExecutionException异常。 
ThreadPoolExecutor.DiscardPolicy		//也是丢弃任务，但是不抛出异常。 
ThreadPoolExecutor.DiscardOldestPolicy	//丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
ThreadPoolExecutor.CallerRunsPolicy		//由调用线程处理该任务
```

#### 二、ThreadPoolExecutor类的使用总结

#### 1. 线程池的任务处理流程

根据 ThreadPoolExecutor 源码前面大段的注释，我们可以看出，当试图通过 excute 方法讲一个 Runnable 任务添加到线程池中时，按照如下顺序来处理：

1. 如果线程池中的线程数量少于 corePoolSize，即使线程池中有空闲线程，也会创建一个新的线程来执行新添加的任务；
2. 如果线程池中的线程数量大于等于 corePoolSize，但缓冲队列 workQueue 未满，则将新添加的任务放到 workQueue 中，按照 FIFO 的原则依次等待执行（线程池中有线程空闲出来后依次将缓冲队列中的任务交付给空闲的线程执行）；
3. 如果线程池中的线程数量大于等于 corePoolSize，且缓冲队列 workQueue 已满，但线程池中的线程数量小于 maximumPoolSize，则会创建新的线程来处理被添加的任务；
4. 如果线程池中的线程数量等于了 maximumPoolSize，有 4 种才处理方式（该构造方法调用了含有 5 个参数的构造方法，并将最后一个构造方法为 RejectedExecutionHandler 类型，它在处理线程溢出时有 4 种方式，这里不再细说，要了解的，自己可以阅读下源码）。

**总结起来，也即是说，当有新的任务要处理时，先看线程池中的线程数量是否大于 corePoolSize，再看缓冲队列 workQueue 是否满，最后看线程池中的线程数量是否大于 maximumPoolSize，都被拒绝才产生线程溢出。**

**另外，当线程池中的线程数量大于 corePoolSize 时，如果里面有线程的空闲时间超过了 keepAliveTime，就将其移除线程池，这样，可以动态地调整线程池中线程的数量。**

#### 2. 线程池状态

> 在ThreadPoolExecutor中定义了一个volatile变量，另外定义了几个static final变量表示线程池的各个状态：

```java
volatile int runState;
static final int RUNNING    = 0;
static final int SHUTDOWN   = 1;
static final int STOP       = 2;
static final int TERMINATED = 3;
```

- runState表示当前线程池的状态，它是一个volatile变量用来保证线程之间的可见性；


- 下面的几个static final变量表示runState可能的几个取值。


- 当创建线程池后，初始时，线程池处于RUNNING状态；


- 如果调用了shutdown()方法，则线程池处于SHUTDOWN状态，此时线程池不能够接受新的任务，它会等待所有任务执行完毕；


- 如果调用了shutdownNow()方法，则线程池处于STOP状态，此时线程池不能接受新的任务，并且会去尝试终止正在执行的任务；


- 当线程池处于SHUTDOWN或STOP状态，并且所有工作线程已经销毁，任务缓存队列已经清空或执行结束后，线程池被设置为TERMINATED状态。

#### 3. 线程池中线程的初始化

默认情况下，创建线程池之后，线程池中是没有线程的，需要提交任务之后才会创建线程。

在实际中如果需要线程池创建之后立即创建线程，可以通过以下两个方法办到：

- prestartCoreThread()：初始化一个核心线程；
- prestartAllCoreThreads()：初始化所有核心线程

　　下面是这2个方法的实现：

```java
public boolean prestartCoreThread() {
    return addIfUnderCorePoolSize(null); //注意传进去的参数是null
}
 
public int prestartAllCoreThreads() {
    int n = 0;
    while (addIfUnderCorePoolSize(null))//注意传进去的参数是null
        ++n;
    return n;
}
```

#### 4. 线程池的关闭

ThreadPoolExecutor提供了两个方法，用于线程池的关闭，分别是shutdown()和shutdownNow()，其中：

- shutdown()：不会立即终止线程池，而是要等所有任务缓存队列中的任务都执行完后才终止，但再也不会接受新的任务
- shutdownNow()：立即终止线程池，并尝试打断正在执行的任务，并且清空任务缓存队列，返回尚未执行的任务

#### 5. 线程池容量的动态调整

ThreadPoolExecutor提供了动态调整线程池容量大小的方法：setCorePoolSize()和setMaximumPoolSize()，

- setCorePoolSize：设置核心池大小
- setMaximumPoolSize：设置线程池最大能创建的线程数目大小

当上述参数从小变大时，ThreadPoolExecutor进行线程赋值，还可能立即创建新的线程来执行任务。

#### 6. 应用程序编程接口（API）

> API	java.util.concurrent.ThreadPoolExecutor

| Modifier and Type        | Method and Description                                       |
| ------------------------ | ------------------------------------------------------------ |
| protected void           | **afterExecute**(Runnable r, Throwable t)Method invoked upon completion of execution of the given Runnable. |
| void                     | **allowCoreThreadTimeOut**(boolean value)Sets the policy governing whether core threads may time out and terminate if no tasks arrive within the keep-alive time, being replaced if needed when new tasks arrive. |
| boolean                  | **allowsCoreThreadTimeOut**()Returns true if this pool allows core threads to time out and terminate if no tasks arrive within the keepAlive time, being replaced if needed when new tasks arrive. |
| boolean                  | **awaitTermination**(long timeout, TimeUnit unit)Blocks until all tasks have completed execution after a shutdown request, or the timeout occurs, or the current thread is interrupted, whichever happens first. |
| protected void           | **beforeExecute**(Thread t, Runnable r)Method invoked prior to executing the given Runnable in the given thread. |
| void                     | **execute**(Runnable command)Executes the given task sometime in the future. |
| protected void           | **finalize**()Invokes shutdown when this executor is no longer referenced and it has no threads. |
| int                      | **getActiveCount**()Returns the approximate number of threads that are actively executing tasks. |
| long                     | **getCompletedTaskCount**()Returns the approximate total number of tasks that have completed execution. |
| int                      | **getCorePoolSize**()Returns the core number of threads.     |
| long                     | **getKeepAliveTime**(TimeUnit unit)Returns the thread keep-alive time, which is the amount of time that threads in excess of the core pool size may remain idle before being terminated. |
| int                      | **getLargestPoolSize**()Returns the largest number of threads that have ever simultaneously been in the pool. |
| int                      | **getMaximumPoolSize**()Returns the maximum allowed number of threads. |
| int                      | **getPoolSize**()Returns the current number of threads in the pool. |
| BlockingQueue<Runnable>  | **getQueue**()Returns the task queue used by this executor.  |
| RejectedExecutionHandler | **getRejectedExecutionHandler**()Returns the current handler for unexecutable tasks. |
| long                     | **getTaskCount**()Returns the approximate total number of tasks that have ever been scheduled for execution. |
| ThreadFactory            | **getThreadFactory**()Returns the thread factory used to create new threads. |
| boolean                  | **isShutdown**()Returns true if this executor has been shut down. |
| boolean                  | **isTerminated**()Returns true if all tasks have completed following shut down. |
| boolean                  | **isTerminating**()Returns true if this executor is in the process of terminating after [shutdown()](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ThreadPoolExecutor.html#shutdown()) or [shutdownNow()](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ThreadPoolExecutor.html#shutdownNow()) but has not completely terminated. |
| int                      | **prestartAllCoreThreads**()Starts all core threads, causing them to idly wait for work. |
| boolean                  | **prestartCoreThread**()Starts a core thread, causing it to idly wait for work. |
| void                     | **purge**()Tries to remove from the work queue all [Future](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html) tasks that have been cancelled. |
| boolean                  | **remove**(Runnable task)Removes this task from the executor's internal queue if it is present, thus causing it not to be run if it has not already started. |
| void                     | **setCorePoolSize**(int corePoolSize)Sets the core number of threads. |
| void                     | **setKeepAliveTime**(long time, TimeUnit unit)Sets the time limit for which threads may remain idle before being terminated. |
| void                     | **setMaximumPoolSize**(int maximumPoolSize)Sets the maximum allowed number of threads. |
| void                     | **setRejectedExecutionHandler**(RejectedExecutionHandler handler)Sets a new handler for unexecutable tasks. |
| void                     | **setThreadFactory**(ThreadFactory threadFactory)Sets the thread factory used to create new threads. |
| void                     | **shutdown**()Initiates an orderly shutdown in which previously submitted tasks are executed, but no new tasks will be accepted. |
| List<Runnable>           | **shutdownNow**()Attempts to stop all actively executing tasks, halts the processing of waiting tasks, and returns a list of the tasks that were awaiting execution. |
| protected void           | **terminated**()Method invoked when the Executor has terminated. |
| String                   | **toString**()Returns a string identifying this pool, as well as its state, including indications of run state and estimated worker and task counts. |



[GitHub传送门](https://github.com/thornshell)