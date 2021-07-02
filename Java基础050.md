# Java并发编程

---

### 为什么很重要

并发编程可以重复利用计算机的资源，把计算机的性能发挥到最大，提供更多效率，最大程度的节约成本。

## 1. 什么是高并发

### 并发VS并行的区别

- 并发：concurrency，多线程“同时”操作同一个资源，并不是真正的同时操作，而是交替操作，单核CPU的情况下，资源按照时间段分配给多个线程。张三李四王五使用一口锅，交替使用。
- 并行：parallelism，是真正的多个线程同时执行，多核CPU，每个线程使用一个CPU资源来运行。张三李四王五使用三口锅，同时进行。

并发-->系统允许多个任务，可以在重叠的时间段内执行的一种设计结构

> 并发编程描述的是一种可以使系统允许多个任务可以在重叠的时间段内执行的设计结构，不是指多个任务在同一时间段内执行，而是指系统具备处理多个任务在同一时间段内同时执行的能力。

高并发使指我们设计的程序，可以支持海量任务的执行在时间段上的重叠的情况。

### 高并发的标准

- QPS：每秒响应HTTP请求数量，QPS不是并发数（在某一时刻，多少请求过来）
- 吞吐量：单位时间内处理的请求数，由QPS和并发数来决定。
- 平均响应时间：系统对一个请求作出响应的平均时间

QPS=并发数/平均响应时间

- 并发用户数：同时承载正常使用系统的用户人数。

### 提高系统并发能力的方式

互联网分布式架构设计，提高系统并发能力的方式：

- 垂直扩展
- 水平扩展

#### 垂直扩展

提升单机处理能力：

##### 提升单机的硬件设备

增加CPU的核数，升级网卡，硬板扩容，升级内存。

##### 提升单机的架构性能

（软件性能），使用cache（缓存）提高效率（减少数据库访问），使用异步请求来增加单服务吞吐量，NoSQL提升数据库访问能力。

#### 水平扩展

##### 集群

一个厨师搞不定，多雇几个厨师一起炒菜。

1. 站点层扩展

Nginx反向代理，多个Tomcat，负载均衡。一个Tomcat跑不动，那就十个Tomcat去跑。集群。

##### 分布式

给厨师雇两个助手，一个负责洗菜，一个负责切菜，厨师只负责炒菜，一个事情拆分成多个步骤，由不同的人去完成。

2. 服务层扩展

RPC框架实现远程调用，Spring Cloud/Spring Boot，Dubbo，分布式架构，将业务逻辑拆分到不同的RPC Client，各自完成对应的业务，如果某项业务并发量很大，添加新的RPC Client，就能扩展服务层的性能，做到理论上的无限高并发。

3. 数据层扩展

在数据量很大的情况下，将原来的一台数据库服务器，拆分成多台，以达到扩充系统性能的目的，主从复制，读写分离，分表分库。

## 2. JUC

JDK提供的一个工具包，专门用来帮助开发者完成Java并发编程。

### 进程和线程

进程是正在运行的程序（动态的），启动的应用程序。

Java中默认的线程数，2个

- main主线程
- GC垃圾回收机制

Java本身是无法开启线程的，Java无法操作硬件，只能通过调用本地方法，C++编写的动态函数库。

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
```

本地方法

```java
private native void start0();
```

### Java中实现多线程有几种方式？

- 继承Thread类
- 实现Runnable接口：run方法没有返回值、java.lang包里
- 实现Callable接口：call方法有返回值、java.util.concurrent包里（JUC）

Runnable --> RunnableFuture --> FutureTask

FutureTask是Runnable的实现类

![Threading08](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading08.svg)

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class Test {
  public static void main(String[] args) {
    MyCallable myCallable = new MyCallable();
    FutureTask<String> futureTask = new FutureTask<>(myCallable);
    new Thread(
      futureTask
    ).start();
    try {
      String value = futureTask.get();
      System.out.println(value);
    } catch (InterruptedException e) {
      e.printStackTrace();
    } catch (ExecutionException e) {
      e.printStackTrace();
    }
  }
}

class MyCallable implements Callable<String> {

  @Override
  public String call() throws Exception {
    System.out.println("Callable");
    return "Hello";
  }
}
```

### 为什么使用JUC的Callable和FutureTask

Callable可以抛异常，可以拿到一个FutureTask对象 --> 线程属于异步计算模型，因此我们没有办法从正在运行的线程当中去拿到函数的返回值，必须等线程完成之后拿到。这种情况下我们可以监视目标线程调用call方法的情况。get方法会阻塞线程 -->异步地获取执行结果。FutureTask可以控制异步任务，对Runnable的一个继承和扩展。get一般放到后面去调用（不然一上来就阻塞）。

Callable有缓存

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Test2 {
  public static void main(String[] args) {
    Account2 account2 = new Account2();
    //        new Thread(new Runnable() {
    //            @Override
    //            public void run() {
    //                account2.count();
    //            }
    //        }, "A").start();
    //        new Thread(new Runnable() {
    //            @Override
    //            public void run() {
    //                account2.count();
    //            }
    //        }, "B").start();
    new Thread(account2::count, "A").start();
    new Thread(() -> {
      account2.count();
    }, "B").start();
  }
}

class Account2 {
  private static int num = 0;
  private Lock lock = new ReentrantLock();

  public void count() {
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    lock.lock();
    num++;
    System.out.println(Thread.currentThread().getName()+"是当前第"+num+"访客");
    lock.unlock();
  }
}
```

## 3. sleep和wait

sleep是让当前线程休眠，wait是让访问当前对象的线程休眠。

sleep不会释放锁，wait会释放锁

## 4.synchronized锁定的是什么

1. synchronized修饰非静态方法，锁定方法的调用者

   ```java
   import java.util.concurrent.TimeUnit;
   
   public class Test {
     public static void main(String[] args) {
       // synchronized锁的是data
       //        Data data = new Data();
       //        new Thread(data::func1).start();
       //        new Thread(data::func2).start();
   
       //        Data data = new Data();
       //        Data data1 = new Data();
       //        new Thread(data::func1).start();
       //        new Thread(data1::func2).start();
   
       Data data = new Data();
       new Thread(data::func1).start();
       new Thread(data::func3).start();
     }
   }
   
   class Data {
     // synchronized锁的是Data实例对象
     public synchronized void func1() {
       try {
         TimeUnit.SECONDS.sleep(3);
       } catch (InterruptedException e) {
         e.printStackTrace();
       }
       System.out.println("func1......");
     }
   
     public synchronized void func2() {
       System.out.println("func2......");
     }
   
     public void func3() {
       System.out.println("func3......");
     }
   }
   ```

2. synchronized修饰静态方法，锁定的是类
3. synchronized静态方法和实例方法同时存在，静态方法锁定的是类，实例方法锁定的是对象
