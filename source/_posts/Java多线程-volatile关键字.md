---
title: Java多线程-volatile关键字
date: 2019-01-18 10:05:34
tags:
	- java
categories:
	- java
---

# volatile关键字

## 一 简介

**Java中的“volatile关键字”：**

在 JDK1.2 之前，Java的内存模型实现总是从**主存（即共享内存）读取变量**，是不需要进行特别的注意的。而在当前的 Java 内存模型下，线程可以把变量保存**本地内存**（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。 

![](/uploads/190118java1/1.png)

要解决这个问题，就需要把变量声明为 **volatile**，这就指示 JVM，这个变量是不稳定的，每次使用它都到主存中进行读取。 

![](/uploads/190118java1/2.png)

## 二 volatile关键字的可见性

**volatile 修饰的成员变量**在每次被线程访问时，都强迫**从主存（共享内存）中重读该成员变量的值**。而且，当成员变量发生变化时，**强迫线程将变化值回写到主存（共享内存）**。这样在任何时刻，**两个不同的线程总是看到某个成员变量的同一个值**，这样也就保证了同步数据的**可见性**。

RunThread.java

```
/**
 * Created by binzhang on 19/1/16.
 */
public class RunThread extends Thread{
    private boolean isRunning = true;
    int m;
    public boolean isRunning() {
        return isRunning;
    }
    public void setRunning(boolean isRunning) {
        this.isRunning = isRunning;
    }
    @Override
    public void run() {
        System.out.println("进入run了");
        while (isRunning == true) {
            int a=2;
            int b=3;
            int c=a+b;
            m=c;
        }
        System.out.println(m);
        System.out.println("线程被停止了！");
    }
}
```

Run.java

```
/**
 * Created by binzhang on 19/1/16.
 */
public class Run {
    public static void main(String[] args) throws InterruptedException {
        RunThread thread = new RunThread();

        thread.start();
        Thread.sleep(1000);
        thread.setRunning(false);

        System.out.println("已经赋值为false");
    }
}
```

运行结果：

![](/uploads/190118java1/3.png)

RunThread类中的isRunning变量没有加上**volatile关键字**时，运行以上代码会出现**死循环**，这是因为isRunning变量虽然被修改但是没有被写到**主存**中，这也就导致该线程在**本地内存中**的值一直为true，这样就导致了死循环的产生。

解决办法也很简单：isRunning变量前加上**volatile关键字**即可。 

```
volatile private boolean isRunning = true;
```
 
这样运行就不会出现死循环了。 
加上volatile关键字后的运行结果： 

![](/uploads/190118java1/4.png)

奇怪的是如果我们在while循环代码里加上任意一个输出语句或者sleep方法你会发现死循环也会停止，不管isRunning变量是否被加上volatile关键字。

![](/uploads/190118java1/5.png)

这是为什么呢？

**因为：JVM会尽力保证内存的可见性，即使这个变量没有加同步关键字**。换句话说，只要CPU有时间，JVM会尽力去保证变量值的更新。这种与volatile关键字的不同在于，volatile关键字会强制的保证线程的可见性。而不加这个关键字，JVM也会尽力去保证可见性，但是如果CPU一直有其他的事情在处理，它也没办法。最开始的代码，一直处于死循环中，CPU处于一直占用的状态，这个时候CPU没有时间，JVM也不能强制要求CPU分点时间去取最新的变量值。**而加了输出或者sleep语句之后，CPU就有可能有时间去保证内存的可见性，于是while循环可以被终止**。

## 三 volatile关键字能保证原子性吗？

没有确切的说法，《Java并发编程艺术》这本书上说保证但是在自增操作（非原子操作）上不保证，《Java多线程编程核心艺术》这本书说不保证。不过应该是**volatile无法保证对变量原子性的，要保证数据的原子性还是要使用synchronized关键字**。

## 四 synchronized关键字和volatile关键字比较

**volatile关键字**是线程同步的**轻量级实现**，所以**volatile性能肯定比synchronized关键字要好**。但是**volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块**。synchronized关键字在JavaSE1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的偏向锁和轻量级锁以及其它各种优化之后执行效率有了显著提升，**实际开发中使用synchronized关键字还是更多一些**。

**多线程访问volatile关键字不会发生阻塞，而synchronized关键字可能会发生阻塞**

**volatile关键字能保证数据的可见性，但不能保证数据的原子性。synchronized关键字两者都能保证。**

**volatile关键字用于解决变量在多个线程之间的可见性，而ynchronized关键字解决的是多个线程之间访问资源的同步性。**



