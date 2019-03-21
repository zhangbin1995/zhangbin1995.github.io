---
title: Java多线程-wait/notify机制
date: 2019-01-18 10:30:01
tags:
	- java
categories:
	- java
---

# 等待/通知（wait/notify）机制

## 一 等待/通知机制介绍

### 1.1 不使用等待/通知机制

当两个线程之间存在**生产和消费者关系**，也就是说**第一个线程（生产者）做相应的操作然后第二个线程（消费者）感知到了变化又进行相应的操作**。比如像下面的whie语句一样，假设这个value值就是第一个线程操作的结果，doSomething()是第二个线程要做的事，当满足条件value=desire后才执行doSomething()。

但是这里有个问题就是：第二个语句不停的通过轮询机制来检测判断条件是否成立。**如果轮询时间的间隔太小会浪费CPU资源，轮询时间的间隔太大，就可能取不到自己想要的数据。所以这里就需要我们今天讲到的等待/通知（wait/notify）机制来解决这两个矛盾**。

    while(value=desire){
        doSomething();
    }

### 1.2 什么是等待/通知机制？

**通俗来讲：**

等待/通知机制在我们生活中比比皆是，一个形象的例子就是厨师和服务员之间就存在等待/通知机制。
 
> * 1. 厨师做完一道菜的时间是不确定的，所以菜到服务员手中的时间是不确定的； 
> * 2. 服务员就需要去“等待（wait）”； 
> * 3. 厨师把菜做完之后，按一下铃，这里的按铃就是“通知（nofity）”； 
> * 4. 服务员听到铃声之后就知道菜做好了，他可以去端菜了。

**用专业术语讲：**

等待/通知机制，是指一个线程A调用了对象O的**wait()方法**进入**等待状态**，而另一个线程B调用了对象O的**notify()/notifyAll()方法**，线程A收到通知后退出**等待队列**，进入可运行状态，进而执行后续操作。上诉两个线程通过对象O来完成交互，而对象上的**wait()方法**和**notify()/notifyAll()方法**的关系就如同开关信号一样，用来完成等待方和通知方之间的交互工作。

### 1.3 等待/通知机制的相关方法

**notify()**	随机唤醒等待队列中等待同一共享资源的 **“一个线程”**，并使该线程退出等待队列，进入可运行状态，也就是**notify()方法仅通知“一个线程”**.

**notifyAll()**	使所有正在等待队列中等待同一共享资源的 **“全部线程”** 退出等待队列，进入可运行状态。此时，优先级最高的那个线程最先执行，但也有可能是随机执行，这取决于JVM虚拟机的实现.

**wait()**	使调用该方法的线程释放共享资源锁，然后从运行状态退出，进入等待队列，直到被再次唤醒

**wait(long)**	超时等待一段时间，这里的参数时间是毫秒，也就是等待长达n毫秒，如果没有通知就超时返回.

**wait(long，int)**	对于超时时间更细力度的控制，可以达到纳秒.

## 二 等待/通知机制的实现

### 2.1 我的第一个等待/通知机制程序

MyList.java

```
import java.util.ArrayList;
import java.util.List;
/**
 * Created by binzhang on 19/1/16.
 */
public class MyList {
    private static List<String> list = new ArrayList<String>();

    public static void add() {
        list.add("anyString");
    }

    public static int size() {
        return list.size();
    }
}
```

ThreadA.java

```
/**
 * Created by binzhang on 19/1/16.
 */
public class ThreadA extends Thread {

    private Object lock;

    public ThreadA(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            synchronized (lock) {
                if (MyList.size() != 5) {
                    System.out.println("wait begin "
                            + System.currentTimeMillis());
                    lock.wait();
                    System.out.println("wait end  "
                            + System.currentTimeMillis());
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

ThreadB.java

```
/**
 * Created by binzhang on 19/1/16.
 */
public class ThreadB extends Thread {
    private Object lock;

    public ThreadB(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        try {
            synchronized (lock) {
                for (int i = 0; i < 10; i++) {
                    MyList.add();
                    if (MyList.size() == 5) {
                        lock.notify();
                        System.out.println("已发出通知！");
                    }
                    System.out.println("添加了" + (i + 1) + "个元素!");
                    Thread.sleep(1000);
                }
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

Run.java

```
/**
 * Created by binzhang on 19/1/16.
 */
public class Run {

    public static void main(String[] args) {

        try {
            Object lock = new Object();
            ThreadA a = new ThreadA(lock);
            a.start();
            Thread.sleep(50);
            ThreadB b = new ThreadB(lock);
            b.start();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行结果：

![](/uploads/190118java2/1.png)

可以看到：**nofify()执行后并不会立即释放锁**。

**synchronized关键字**可以将任何一个Object对象作为同步对象来看待，而**Java为每个Object都实现了等待/通知（wait/notify）机制的相关方法**，它们必须用在synchronized关键字同步的Object的临界区内。通过调用**wait()方法**可以使处于临界区内的线程进入**等待状态**，同时**释放被同步对象的锁**。而notify()方法可以唤醒一个因调用wait操作而处于阻塞状态中的线程，使其进入就绪状态。被重新唤醒的线程会视图重新获得临界区的控制权也就是锁，并继续执行wait方法之后的代码。如果发出notify操作时没有处于阻塞状态中的线程，那么该命令会被忽略。

如果我们这里不通过**等待/通知（wait/notify）机制**实现，而是使用如下的**while循环**实现的话，我们上面也讲过会有很大的弊端。

```
 while(MyList.size() == 5){
        doSomething();
    }
```

### 2.2 线程的基本状态

上面我们已经提到了和线程有关的大部分API，这些API可以改变线程对象的状态。如下图所示：

![](/uploads/190118java2/2.png)

>  **新建(new)**：新创建了一个线程对象。 

>  **可运行(runnable)**：线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获 取cpu的使用权。 

>  **运行(running)**：可运行状态(runnable)的线程获得了cpu时间片（timeslice），执行程序代码。 

>  **阻塞(block)**：阻塞状态是指线程因为某种原因放弃了cpu使用权，也即让出了cpu timeslice，暂时停止运行。直到线程进入可运行(runnable)状态，才有 机会再次获得cpu timeslice转到运行(running)状态。阻塞的情况分三种：

> * 等待阻塞：运行(running)的线程执行o.wait()方法，JVM会把该线程放 入等待队列(waitting queue)中。

> * 同步阻塞：运行(running)的线程在获取对象的同步锁时，若该同步锁 被别的线程占用，则JVM会把该线程放入锁池(lock pool)中。

> * 其他阻塞: 运行(running)的线程执行Thread.sleep(long ms)或t.join()方法，或者发出了I/O请求时，JVM会把该线程置为阻塞状态。当sleep()状态超时join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入可运行(runnable)状态。

> **死亡(dead)**：线程run()、main()方法执行结束，或者因异常退出了run()方法，则该线程结束生命周期。死亡的线程不可再次复生。

**备注： 
可以用早起坐地铁来比喻这个过程：**

> * 还没起床：sleeping

> * 起床收拾好了，随时可以坐地铁出发：Runnable

> * 等地铁来：Waiting

> * 地铁来了，但要排队上地铁：I/O阻塞

> * 上了地铁，发现暂时没座位：synchronized阻塞

> * 地铁上找到座位：Running

> * 到达目的地：Dead

### 2.3 notify()锁不释放

**当方法wait()被执行后，锁自动被释放，但执行完notify()方法后，锁不会自动释放。必须执行完notify()方法所在的synchronized代码块后才释放。**

### 2.4 当interrupt方法遇到wait方法

**当线程呈现wait状态时，对线程对象调用interrupt方法会出现interruptedException异常。**

service.java

```
/**
 * Created by binzhang on 19/1/17.
 */
public class Service {
    public void testMethod(Object lock) {
        try {
            synchronized (lock) {
                System.out.println("begin wait()");
                lock.wait();
                System.out.println("  end wait()");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println("出现异常了，因为呈wait状态的线程被interrupt了！");
        }
    }
}
```

ThreadA.java

```
public class ThreadA extends Thread {

    private Object lock;

    public ThreadA(Object lock) {
        super();
        this.lock = lock;
    }

    @Override
    public void run() {
        Service service = new Service();
        service.testMethod(lock);
    }
}
```

Run.java

```
public class Run {

    public static void main(String[] args) {

        try {
            Object lock = new Object();
            ThreadA a = new ThreadA(lock);
            a.start();
            Thread.sleep(5000);
            a.interrupt();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

运行结果：

![](/uploads/190118java2/3.png)


