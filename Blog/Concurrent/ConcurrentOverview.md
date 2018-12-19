# ConcurrentOverview

### 一、线程生命周期

![线程生命周期](C:\Users\Thornhill\Desktop\document\线程生命周期.PNG)



- NEW
- RUNNABLE
- RUNNING
- BLOCKED
- TERMINATED

### 二、start方法

```java
public synchronized void start() {
    /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
    group.add(this);

    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
        }
    }
}

private native void start0();
```

> Causes this thread to begin execution; the Java Virtual Machine calls the run method of this thread.

上面这句话意思是：在开始执行这个线程时，将会调用该线程的run方法，换言之，run方法是被JNI方法start0()调用的，仔细阅读start源码将会总结出如下几个知识要点：

- Thread被构造后的NEW状态，事实上threadStatus这个内部属性为0
- 不能两次启动Thread，否则会出现`IllegalThreadStateException`异常
- 线程启动后将会被加入到一个ThreadGroup中。
- 一个线程生命周期结束，也就是到了TERMINATED状态，再次调用start方法是不允许的，也就是说TERMINATED状态是无法回到RUNNABLE/RUNNING状态的

### 三、线程的父子关系

```java
/**
     * Initializes a Thread.
     *
     * @param g the Thread group
     * @param target the object whose run() method gets called
     * @param name the name of the new Thread
     * @param stackSize the desired stack size for the new thread, or
     *        zero to indicate that this parameter is to be ignored.
     * @param acc the AccessControlContext to inherit, or
     *            AccessController.getContext() if null
     * @param inheritThreadLocals if {@code true}, inherit initial values for
     *            inheritable thread-locals from the constructing thread
     */
private void init(ThreadGroup g, Runnable target, String name,
                  long stackSize, AccessControlContext acc,
                  boolean inheritThreadLocals) {
    if (name == null) {
        throw new NullPointerException("name cannot be null");
    }

    this.name = name;

    Thread parent = currentThread();
    SecurityManager security = System.getSecurityManager();
    if (g == null) {
        /* Determine if it's an applet or not */

        /* If there is a security manager, ask the security manager
               what to do. */
        if (security != null) {
            g = security.getThreadGroup();
        }

        /* If the security doesn't have a strong opinion of the matter
               use the parent thread group. */
        if (g == null) {
            g = parent.getThreadGroup();
        }
    }

    /* checkAccess regardless of whether or not threadgroup is
           explicitly passed in. */
    g.checkAccess();

    /*
         * Do we have the required permissions?
         */
    if (security != null) {
        if (isCCLOverridden(getClass())) {
            security.checkPermission(SUBCLASS_IMPLEMENTATION_PERMISSION);
        }
    }

    g.addUnstarted();

    this.group = g;
    this.daemon = parent.isDaemon();
    this.priority = parent.getPriority();
    if (security == null || isCCLOverridden(parent.getClass()))
        this.contextClassLoader = parent.getContextClassLoader();
    else
        this.contextClassLoader = parent.contextClassLoader;
    this.inheritedAccessControlContext =
        acc != null ? acc : AccessController.getContext();
    this.target = target;
    setPriority(priority);
    if (inheritThreadLocals && parent.inheritableThreadLocals != null)
        this.inheritableThreadLocals =
        ThreadLocal.createInheritedMap(parent.inheritableThreadLocals);
    /* Stash the specified stack size in case the VM cares */
    this.stackSize = stackSize;

    /* Set thread ID */
    tid = nextThreadID();
}
```

上面的代码中的`currentThread()`是获取当前线程，在线程生命周期中，我们说过现成的最初状态是NEW，没有执行start方法之前，他只能算一个Thread实例，并不意味着一个新的线程被创建，因此`currentThread()`代表的将会是创建他的那个线程，因此有：

- 一个线程的创建肯定是由另一个线程完成的
- 被创建线程的父线程是创建他的线程

我们都知道main函数所在线程是由JVM创建的，也就是main线程，那就意味着所有线程的顶级父线程都是main线程。

### 四、守护线程

> The Java Virtual Machine exits when the only threads running are all daemon threads.

在正常情况下，若JVM中没有一个非守护线程，则JVM的进程会退出。

##### 一、什么是守护线程

```java
thread.setDaemon(true);
```

简单的来说，调用setDaemon方法即可，true代表守护线程，false代表正常线程。

- 线程是否为守护线程和他的父线程有很大的关系，如果父线程是正常线程，则子线程也是正常线程，反之亦然，如果你想要修改他的特性可以借助setDaemon方法。

- isDaemon方法可以判断该线程是否为守护线程。
- 另外，setDaemon方法只在线程启动之前才能生效，如果一个线程已经死亡，那么再设置setDaemon则会抛出`IllegalThreadStateException`异常。

##### 二、守护线程的作用

​	守护线程经常用作与执行一些后台任务，因此有时他也被称为后台线程，当你希望关闭某些线程的时候，或者退出JVM进程的时候，一些线程能够自动关闭，此时就可以使用守护线程为你完成这样的工作。



