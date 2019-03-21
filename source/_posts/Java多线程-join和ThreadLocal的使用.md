---
title: Java多线程-join和ThreadLocal的使用
date: 2019-01-18 11:49:38
tags:
	- java
categories:
	- java
---

## Thread.join()的使用

在很多情况下，主线程生成并起动了子线程，如果子线程里要进行大量的耗时的运算，主线程往往将于子线程之前结束，但是如果主线程处理完其他的事务后，需要用到子线程的处理结果，也就是**主线程需要等待子线程执行完成之后再结束，这个时候就要用到join()方法了。另外，一个线程需要等待另一个线程也需要用到join()方法**。

Thread类除了提供**join()方法**之外，还提供了**join(long millis)、join(long millis, int nanos)**两个具有超时特性的方法。这两个超时方法表示，如果线程thread在指定的超时时间没有终止，那么将会从该超时方法中返回。

### 2.1 join方法使用

**不使用join方法的问题：**

Test.java

```
public class Run {

    public static void main(String[] args) throws InterruptedException {

        MyThread threadTest = new MyThread();
        threadTest.start();

        //Thread.sleep(?);//因为不知道子线程要花的时间这里不知道填多少时间
        System.out.println("我想当threadTest对象执行完毕后我再执行");
    }
    static public class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println("我想先执行");
        }
    }
}
```

运行结果：

![](/uploads/190118java3/1.png)

可以看到子线程中后被执行，这里的例子只是一个简单的演示，我们想一下：**假如子线程运行的结果被主线程运行需要怎么办？sleep方法？**当然可以，但是子线程运行需要的时间是不确定的，所以sleep多长时间当然也就不确定了。这里就需要使用join方法解决上面的问题。

**使用join方法解决上面的问题：**

Run.java

```
public class Run {

    public static void main(String[] args) throws InterruptedException {

        MyThread threadTest = new MyThread();
        threadTest.start();

        //Thread.sleep(?);//因为不知道子线程要花的时间这里不知道填多少时间
        threadTest.join();
        System.out.println("我想当threadTest对象执行完毕后我再执行");
    }
    static public class MyThread extends Thread {

        @Override
        public void run() {
            System.out.println("我想先执行");
        }

    }
}
```

上面的代码仅仅加了一句：**threadTest.join()**。在这里join方法的作用就是**主线程需要等待子线程执行完成之后再结束**。

### 2.2 join(long millis)方法的使用

join(long millis)中的参数就是设定的等待时间。

Run.java

```
public class Run {

    public static void main(String[] args) {
        try {
            MyThread threadTest = new MyThread();
            threadTest.start();

            threadTest.join(2000);// 只等2秒
            //Thread.sleep(2000);

            System.out.println("  end timer=" + System.currentTimeMillis());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    static public class MyThread extends Thread {

        @Override
        public void run() {
            try {
                System.out.println("begin Timer=" + System.currentTimeMillis());
                Thread.sleep(10000);
                System.out.println("last Timer=" + System.currentTimeMillis());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}
```

输出结果：

![](/uploads/190118java3/2.png)

可以看到begin和end之间间隔两秒，end和last之间间隔八秒。
而且在main中不管是用threadTest.join(2000)还是Thread.sleep(2000)，begin和end的间隔都是两秒，threadTest.join(2000) 和Thread.sleep(2000) 和区别在于： **Thread.sleep(2000)不会释放锁，threadTest.join(2000)会释放锁** 。

## 三 ThreadLocal的使用

变量值的共享可以使用public static变量的形式，所有线程都使用一个public static变量。**如果想实现每一个线程都有自己的共享变量该如何解决呢？**JDK中提供的**ThreadLocal类**正是为了解决这样的问题。ThreadLocal类主要解决的就是让每个线程绑定自己的值，可以将ThreadLocal类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。

**再举个简单的例子： **
比如有两个人去宝屋收集宝物，这两个共用一个袋子的话肯定会产生争执，但是给他们两个人每个人分配一个袋子的话就不会出现这样的问题。如果把这两个人比作线程的话，那么ThreadLocal就是用来这两个线程竞争的。

**ThreadLocal类相关方法：**
**get()**	返回当前线程的此线程局部变量的副本中的值。
**set(T value)**	将当前线程的此线程局部变量的副本设置为指定的值
**remove()**	删除此线程局部变量的当前线程的值。
**initialValue()**	返回此线程局部变量的当前线程的“初始值”

### 3.1 ThreadLocal类的初试

Test1.java

```
public class Test1 {
    public static ThreadLocal<String> t1 = new ThreadLocal<String>();

    public static void main(String[] args) {
        if (t1.get() == null) {
            System.out.println("为ThreadLocal类对象放入值:aaa");
            t1.set("aaaֵ");
        }
        System.out.println(t1.get());//aaa
        System.out.println(t1.get());//aaa
    }
}
```

运行结果：

```
为ThreadLocal类对象放入值:aaa
aaaֵ
aaaֵ
```

从运行结果可以看出，第一次调用ThreadLocal对象的**get()方法**时返回的值是**null**,通过调用**set()方法**可以为ThreadLocal对象赋值。

如果想要解决get()方法null的问题，可以使用ThreadLocal对象的**initialValue方法**。如下：

Test2.java

```
public class Test2 {
    public static ThreadLocalExt t1 = new ThreadLocalExt();

    public static void main(String[] args) {
        if (t1.get() == null) {
            System.out.println("从未放过值");
            t1.set("我的值");
        }
        System.out.println(t1.get());
        System.out.println(t1.get());
    }
    static public class ThreadLocalExt extends ThreadLocal {
        @Override
        protected Object initialValue() {
            return "我是默认值 第一次get不再为null";
        }
    }
}
```

运行结果：

```
我是默认值 第一次get不再为null
我是默认值 第一次get不再为null
```

### 3.2 验证线程变量间的隔离性

Test3.java

```
import java.util.Date;
/**
 *TODO 验证线程变量间的隔离性
 */
public class Test3 {

    public static void main(String[] args) {
        try {
            for (int i = 0; i < 10; i++) {
                System.out.println("       在Main线程中取值=" + Tools.tl.get());
                Thread.sleep(100);
            }
            Thread.sleep(5000);
            ThreadA a = new ThreadA();
            a.start();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    static public class Tools {
        public static ThreadLocalExt tl = new ThreadLocalExt();
    }
    static public class ThreadLocalExt extends ThreadLocal {
        @Override
        protected Object initialValue() {
            return new Date().getTime();
        }
    }

    static public class ThreadA extends Thread {

        @Override
        public void run() {
            try {
                for (int i = 0; i < 10; i++) {
                    System.out.println("在ThreadA线程中取值=" + Tools.tl.get());
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
        }
    }
}
```

运行结果：

![](/uploads/190118java3/3.png)

从运行结果可以看出子线程和父线程各自拥有各自的值。

### 3.3 InheritableThreadLocal

**ThreadLocal类固然很好，但是子线程并不能取到父线程的ThreadLocal类的变量，InheritableThreadLocal类就是解决这个问题的。**

取父线程的值：

修改Test3.java的内部类Tools 和ThreadLocalExt类如下：

```
 static public class Tools {
        public static InheritableThreadLocalExt tl = new InheritableThreadLocalExt();
    }
    static public class InheritableThreadLocalExt extends InheritableThreadLocal {
        @Override
        protected Object initialValue() {
            return new Date().getTime();
        }
    }
```

运行结果：

![](/uploads/190118java3/4.png)

取父线程的值并修改：

修改Test3.java的内部类Tools和InheritableThreadLocalExt类如下：

```
static public class Tools {
    public static InheritableThreadLocalExt tl = new InheritableThreadLocalExt();
}
static public class InheritableThreadLocalExt extends InheritableThreadLocal {
    @Override
    protected Object initialValue() {
        return new Date().getTime();
    }

    @Override
    protected Object childValue(Object parentValue) {
        return parentValue + " 我在子线程加的~!";
    }
}
```

运行结果：

![](/uploads/190118java3/5.png)

在使用InheritableThreadLocal类需要注意的一点是：**如果子线程在取得值的同时，主线程将InheritableThreadLocal中的值进行更改，那么子线程取到的还是旧值。**


