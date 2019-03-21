---
title: Java多线程-synchronized关键字
date: 2019-01-17 21:19:16
tags:
	- java
categories:
	- java
---

# synchronized关键字

## 一 简介
Java并发编程这个领域中**synchronized关键字**一直都是元老级的角色，很久之前很多人都会称它为**“重量级锁”**。但是，在JavaSE 1.6之后进行了主要包括为了**减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁**以及其它各种优化之后变得在某些情况下并不是那么重了。

本次总结不会介绍synchronized关键字的实现原理，更多的是synchronized关键字的使用。后面在看过《Java并发编程的艺术》之后会再次分析它的实现原理。

本篇博文参考出处:
[https://blog.csdn.net/qq_34337272/article/details/79655194](https://blog.csdn.net/qq_34337272/article/details/79655194)

## 二 变量安全性
**“非线程安全”**问题存在于**“实例变量”**中，如果是**方法内部的私有变量**，则不存在**“非线程安全”**问题，所得结果也就是**“线程安全”**的了。

如果两个线程同时操作对象中的实例变量，则会出现**“非线程安全”**，解决办法就是在方法前加上**synchronized关键字**即可。

## 三 多个对象多个锁
**先看例子：**

HasSelfPrivateNum.java

```
public class HasSelfPrivateNum {

    private int num = 0;

    synchronized public void addI(String username) {
        try {
            if (username.equals("a")) {
                num = 100;
                System.out.println("a set over!");
                //如果去掉hread.sleep(2000)，那么运行结果就会显示为同步的效果
                Thread.sleep(2000);
            } else {
                num = 200;
                System.out.println("b set over!");
            }
            System.out.println(username + " num=" + num);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

ThreadA.java

```
public class ThreadA extends Thread {
    private HasSelfPrivateNum numRef;
    public ThreadA(HasSelfPrivateNum numRef) {
        super();
        this.numRef = numRef;
    }

    @Override
    public void run() {
        super.run();
        numRef.addI("a");
    }
}
```

```
ThreadB.java
public class ThreadB extends Thread {
    private HasSelfPrivateNum numRef;
    public ThreadB(HasSelfPrivateNum numRef) {
        super();
        this.numRef = numRef;
    }

    @Override
    public void run() {
        super.run();
        numRef.addI("b");
    }
}
```

```
Run.java
public class Run {
    public static void main(String[] args) {
        HasSelfPrivateNum numRef1 = new HasSelfPrivateNum();
        HasSelfPrivateNum numRef2 = new HasSelfPrivateNum();
        ThreadA athread = new ThreadA(numRef1);
        athread.start();
        ThreadB bthread = new ThreadB(numRef2);
        bthread.start();
    }
}
```

运行结果：（a num=100停顿一会才执行出来）

![](/uploads/190117java1/1.png)

（按说定义了synchronized，应该等a执行完才执行b的啊，往下看。。。）

上面实例中两个线程ThreadA和ThreadB分别访问同一个类的不同实例的相同名称的同步方法，但是效果确实异步执行。

**为什么会这样呢？**

这是因为**synchronized取得的锁都是对象锁，而不是把一段代码或方法当做锁**。所以在上面的实例中，哪个线程先执行带synchronized关键字的方法，则哪个线程就持有该方法**所属对象的锁Lock**，那么其他线程只能呈等待状态，**前提是多个线程访问的是同一个对象**。本例中很显然是两个对象。

在本例中创建了两个HasSelfPrivateNum类对象，所以就**产生了两个锁**。当ThreadA的引用执行到addI方法中的runThread.sleep(2000)语句时，ThreadB就会**“乘机执行”**。所以才会导致执行结果如上图所示（备注：由于runThread.sleep(2000)，“a num=100”停顿了两秒才输出）

## 四 synchronized方法与锁对象
通过上面我们知道**synchronized取得的锁都是对象锁，而不是把一段代码或方法当做锁**。如果多个线程访问的是同一个对象，哪个线程先执行带synchronized关键字的方法，则哪个线程就持有该方法，那么其他线程只能呈等待状态。如果多个线程访问的是多个对象则不一定，因为多个对象会产生多个锁。

**那么我们思考一下当多个线程访问的是同一个对象中的非synchronized类型方法会是什么效果？**

答案是：会异步调用非synchronized类型方法，解决办法也很简单在非synchronized类型方法前加上synchronized关键字即可。

## 五 脏读

发生脏读的情况是在读取实例变量时，此值已经被其他线程更改过。

PublicVar.java

```
public class PublicVar {

    public String username = "A";
    public String password = "AA";

    synchronized public void setValue(String username, String password) {
        try {
            this.username = username;
            Thread.sleep(5000);
            this.password = password;

            System.out.println("setValue method thread name="
                    + Thread.currentThread().getName() + " username="
                    + username + " password=" + password);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    //该方法前加上synchronized关键字就同步了
    public void getValue() {
        System.out.println("getValue method thread name="
                + Thread.currentThread().getName() + " username=" + username
                + " password=" + password);
    }
}
```

```
ThreadA.java
public class ThreadA extends Thread {

    private PublicVar publicVar;

    public ThreadA(PublicVar publicVar) {
        super();
        this.publicVar = publicVar;
    }

    @Override
    public void run() {
        super.run();
        publicVar.setValue("B", "BB");
    }
}
```

```
Test.java
public class Test {

    public static void main(String[] args) {
        try {
            PublicVar publicVarRef = new PublicVar();
            ThreadA thread = new ThreadA(publicVarRef);
            thread.start();

            Thread.sleep(200);//打印结果受此值大小影响

            publicVarRef.getValue();
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

    }
}
```

运行结果：

![](/uploads/190117java1/2.png)

解决办法：getValue()方法前加上synchronized关键字即可。

加上后运行结果：

![](/uploads/190117java1/3.png)


## 六 synchronized锁重入

**“可重入锁”**概念是：**自己可以再次获取自己的内部锁**。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。

Service.java

```
public class Service {
    synchronized public void service1() {
        System.out.println("service1");
        service2();
    }

    synchronized public void service2() {
        System.out.println("service2");
        service3();
    }

    synchronized public void service3() {
        System.out.println("service3");
    }
}
```

MyThread.java

```
public class MyThread extends Thread {
    @Override
    public void run() {
        Service service = new Service();
        service.service1();
    }
}
```

Run.java

```
public class Run {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

运行结果：

![](/uploads/190117java1/4.png)

另外**可重入锁也支持在父子类继承的环境中**

Main.java

```
public class Main {

    public int i = 10;

    synchronized public void operateIMainMethod() {
        try {
            i--;
            System.out.println("main print i=" + i);
            Thread.sleep(100);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

Sub.java

```
public class Sub extends Main {

    synchronized public void operateISubMethod() {
        try {
            while (i > 0) {
                i--;
                System.out.println("sub print i=" + i);
                Thread.sleep(100);
                this.operateIMainMethod();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

MyThread.java

```
public class MyThread extends Thread {
    @Override
    public void run() {
        Sub sub = new Sub();
        sub.operateISubMethod();
    }
}
```

Run.java

```
public class Run {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

运行结果：

![](/uploads/190117java1/5.png)

说明当存在父子类继承关系时，子类是完全可以通过“可重入锁”调用父类的同步方法。

另外出现异常时，其锁持有的锁会自动释放。

## 七 同步不具有继承性

如果父类有一个带synchronized关键字的方法，子类继承并重写了这个方法。
但是同步不能继承，所以还是需要在子类方法中添加synchronized关键字。

# synchronized同步语句块

## 一 synchronized方法的缺点

使用**synchronized关键字**声明方法有些时候是有很大的弊端的，比如我们有两个线程一个线程A调用同步方法后获得锁，那么另一个线程B就需要等待A执行完，但是如果说A执行的是一个很费时间的任务的话这样就会很耗时。
先来看一个**暴露synchronized方法的缺点实例**，然后在看看如何通过**synchronized同步语句块**解决这个问题。

Task.java

```
public class Task {

    private String getData1;
    private String getData2;

    public synchronized void doLongTimeTask() {
        try {
            System.out.println("begin task");
            Thread.sleep(3000);
            getData1 = "长时间处理任务后从远程返回的值1 threadName="
                    + Thread.currentThread().getName();
            getData2 = "长时间处理任务后从远程返回的值2 threadName="
                    + Thread.currentThread().getName();
            System.out.println(getData1);
            System.out.println(getData2);
            System.out.println("end task");
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

CommonUtils.java

```
public class CommonUtils {

    public static long beginTime1;
    public static long endTime1;

    public static long beginTime2;
    public static long endTime2;
}
```

MyThread1.java

```
public class MyThread1 extends Thread {
    private Task task;
    public MyThread1(Task task) {
        super();
        this.task = task;
    }
    @Override
    public void run() {
        super.run();
        CommonUtils.beginTime1 = System.currentTimeMillis();
        task.doLongTimeTask();
        CommonUtils.endTime1 = System.currentTimeMillis();
    }
}
```

MyThread2.java

```
public class MyThread2 extends Thread {
    private Task task;
    public MyThread2(Task task) {
        super();
        this.task = task;
    }
    @Override
    public void run() {
        super.run();
        CommonUtils.beginTime2 = System.currentTimeMillis();
        task.doLongTimeTask();
        CommonUtils.endTime2 = System.currentTimeMillis();
    }
}
```

Run.java

```
public class Run {

    public static void main(String[] args) {
        Task task = new Task();

        MyThread1 thread1 = new MyThread1(task);
        thread1.start();

        MyThread2 thread2 = new MyThread2(task);
        thread2.start();

        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        long beginTime = CommonUtils.beginTime1;
        if (CommonUtils.beginTime2 < CommonUtils.beginTime1) {
            beginTime = CommonUtils.beginTime2;
        }

        long endTime = CommonUtils.endTime1;
        if (CommonUtils.endTime2 > CommonUtils.endTime1) {
            endTime = CommonUtils.endTime2;
        }

        System.out.println("耗时：" + ((endTime - beginTime) / 1000));
    }
}
```

运行结果：

![](/uploads/190117java1/6.png)

从运行时间来看，synchronized方法的问题很明显。可以**使用synchronized同步块**来解决这个问题。但是要注意synchronized同步块的使用方法，如果synchronized同步块使用不好的话并不会带来效率的提升。

## 二 synchronized（this）同步代码块的使用

修改上例中的Task.java如下：

```
public class Task {

    private String getData1;
    private String getData2;

    public void doLongTimeTask() {
        try {
            System.out.println("begin task");
            Thread.sleep(3000);

            String privateGetData1 = "长时间处理任务后从远程返回的值1 threadName="
                    + Thread.currentThread().getName();
            String privateGetData2 = "长时间处理任务后从远程返回的值2 threadName="
                    + Thread.currentThread().getName();

            synchronized (this) {
                getData1 = privateGetData1;
                getData2 = privateGetData2;
            }

            System.out.println(getData1);
            System.out.println(getData2);
            System.out.println("end task");
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

运行结果：

![](/uploads/190117java1/7.png)

从上面代码可以看出**当一个线程访问一个对象的synchronized同步代码块时，另一个线程仍然可以访问该对象非synchronized同步代码块**。

时间虽然缩短了，但是大家考虑一下synchronized代码块真的是同步的吗？它真的持有当前调用对象的锁吗？

**是的。不在synchronized代码块中就异步执行，在synchronized代码块中就是同步执行。**

## 三 synchronized（object）代码块间使用

MyObject.java

```
public class MyObject {
}
```

Service.java

```
public class Service {

    public void testMethod1(MyObject object) {
        synchronized (object) {
            try {
                System.out.println("testMethod1 ____getLock time="
                        + System.currentTimeMillis() + " run ThreadName="
                        + Thread.currentThread().getName());
                Thread.sleep(2000);
                System.out.println("testMethod1 releaseLock time="
                        + System.currentTimeMillis() + " run ThreadName="
                        + Thread.currentThread().getName());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

ThreadA.java

```
public class ThreadA extends Thread {
    private Service service;
    private MyObject object;

    public ThreadA(Service service, MyObject object) {
        super();
        this.service = service;
        this.object = object;
    }

    @Override
    public void run() {
        super.run();
        service.testMethod1(object);
    }
}
```

ThreadB.java

```
public class ThreadB extends Thread {
    private Service service;
    private MyObject object;

    public ThreadB(Service service, MyObject object) {
        super();
        this.service = service;
        this.object = object;
    }

    @Override
    public void run() {
        super.run();
        service.testMethod1(object);
    }
}
```

Run.java

```
public class Run {

    public static void main(String[] args) {
        Service service = new Service();
        MyObject object = new MyObject();

        ThreadA a = new ThreadA(service, object);
        a.setName("a");
        a.start();

        ThreadB b = new ThreadB(service, object);
        b.setName("b");
        b.start();
    }
}
```

运行结果：

![](/uploads/190117java1/8.png)


可以看出如下图所示，**两个线程使用了同一个“对象监视器”，所以运行结果是同步的**。

![](/uploads/190117java1/9.png)

那么如果使用不同的对象监视器会出现什么情况呢？

修改Run.java：

```
public class Run {

    public static void main(String[] args) {
        Service service = new Service();
        MyObject object1 = new MyObject();
        MyObject object2 = new MyObject();

        ThreadA a = new ThreadA(service, object1);
        a.setName("a");
        a.start();

        ThreadB b = new ThreadB(service, object2);
        b.setName("b");
        b.start();
    }
}
```

运行结果：

![](/uploads/190117java1/10.png)


可以看出如下图所示，两个线程使用了不同的“对象监视器”，所以运行结果就不是同步的了。

![](/uploads/190117java1/11.png)

## 四 synchronized代码块间的同步性

当一个对象访问synchronized(this)代码块时，其他线程对同一个对象中所有其他synchronized(this)代码块代码块的访问将被阻塞，这说明**synchronized(this)代码块使用的“对象监视器”是一个**。 
也就是说**和synchronized方法一样，synchronized(this)代码块也是锁定当前对象的**。

另外通过上面的学习我们可以得出**两个结论**。
  1. 其他线程执行对象中synchronized同步方法和synchronized(this)代码块时呈现同步效果;
  2. 如果两个线程使用了同一个“对象监视器”,运行结果同步，否则不同步.

## 五 静态同步synchronized方法与synchronized(class)代码块

**synchronized关键字加到static静态方法和synchronized(class)代码块上都是是给Class类上锁，而synchronized关键字加到非static静态方法上是给对象上锁。**

Service.java

```
public class Service {

    public static void printA() {
        synchronized (Service.class) {
            try {
                System.out.println(
                        "线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printA");
                Thread.sleep(3000);
                System.out.println(
                        "线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printA");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    synchronized public static void printB() {
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printB");
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printB");
    }

    synchronized public void printC() {
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "进入printC");
        System.out.println("线程名称为：" + Thread.currentThread().getName() + "在" + System.currentTimeMillis() + "离开printC");
    }
}
```

ThreadA.java

```
public class ThreadA extends Thread {
    private Service service;
    public ThreadA(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printA();
    }
}
```

ThreadB.java

```
public class ThreadB extends Thread {
    private Service service;
    public ThreadB(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printB();
    }
}
```

ThreadC.java

```
public class ThreadB extends Thread {
    private Service service;
    public ThreadB(Service service) {
        super();
        this.service = service;
    }
    @Override
    public void run() {
        service.printB();
    }
}
```

Run.java

```
public class Run {
    public static void main(String[] args) {
        Service service = new Service();
        ThreadA a = new ThreadA(service);
        a.setName("A");
        a.start();

        ThreadB b = new ThreadB(service);
        b.setName("B");
        b.start();

        ThreadC c = new ThreadC(service);
        c.setName("C");
        c.start();
    }
}
```

运行结果：

![](/uploads/190117java1/12.png)

从运行结果可以看出:静态同步synchronized方法与synchronized(class)代码块持有的锁一样，都是**Class锁，Class锁对对象的所有实例起作用。synchronized关键字加到非static静态方法上持有的是对象锁**。
线程A,B和线程C持有的锁不一样，所以A和B运行同步，但是和C运行不同步。 

![](/uploads/190117java1/13.png)

## 六 数据类型String的常量池属性

**在Jvm中具有String常量池缓存的功能**

```
    String s1 = "a";
    String s2 = "a";
    System.out.println(s1==s2);//true
```

上面代码输出为true.**这是为什么呢？**

**字符串常量池中的字符串只存在一份！ 即执行完第一行代码后，常量池中已存在 “a”，那么s2不会在常量池中申请新的空间，而是直接把已存在的字符串内存地址返回给s2。**

因为数据类型String的常量池属性，所以synchronized(string)在使用时某些情况下会出现一些问题，比如两个线程运行 

```
synchronized(“abc”)｛ 
｝和 
synchronized(“abc”)｛ 
｝
```

修饰的方法时，这两个线程就会持有相同的锁，导致某一时刻只有一个线程能运行。所以尽量不要使用synchronized(string)而使用synchronized(object)




