### 多线程

多线程是提升程序性能非常重要的一种方式，必须掌握的技术。

使用多线程可以让程序充分利用CPU的资源。

优点

- 系统资源得到更合理的利用。
- 程序设计更加简洁。
- 程序相应更快、运行效率更高。

缺点

- 需要更多的内存空间来支持多线程
- 多线程并发访问的情况可能会影响数据的准确性。
- 数据被多线程共享，可能会出现死锁的情况。

多线程并发->数据不准确->线程同步解决-> 死锁

### 进程和线程

什么是进程：进程就是计算机正在运行的一个独立的应用程序。

进程是一个动态的概念，当我们启动某个应用程序的时候，进程就产生了，当我们关闭应用的时候，进程就结束了，进程的生命周期就是我们在使用该软件的整个过程。一个应用程序可能有多于一个进程。

什么是线程？ 进程和线程都是程序运行过程中的概念。

线程是组成进程的基本单位，可以完成特定的功能，一个进程是由一个或多个线程组成的。

应用程序是静态的，进程和线程是动态的，有创建有销毁，存在是暂时的，不是永久的。

进程和线程的区别

进程在运行时拥有独立的内存空间，即每个进程所占用的内存空间都是独立的，互不干扰。

线程是共享内存空间的，但是每个线程的执行都是互相独立的，单独的线程是无法执行的，由进程来控制多个线程的执行。

### 多线程

多线程是指在一个进程中，多个线程同时执行，这里说的同时执行并不是真正意义的同时执行。

系统会为每个线程分配CPU资源，在某个具体的时间段内CPU资源会被一个线程占用，在不同的时间段内由不同的线程来占用CPU资源，**所以多个线程还是在交替执行，只不过因为CPU运行速度太快，我们感觉是在同时执行**

![Threading01](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading01.svg)

整个程序如果是一条回路，说明程序只有一个线程

![Threading02](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading02.svg)

程序有两条回路，同时向下执行，这种情况就是多线程，两个线程同时在执行。

### Java中线程的使用

可以用匿名内部类

```java
new Thread(new Runnable() {
  @Override
  public void run() {
    for (int i = 0; i < 100; i++) {
      System.out.println("-----王五-----");
    }
  }
}).start();
```

Java中使用线程有两种方式：

- 继承Thread类
- 实现Runnable接口

Java写程序三部分组成：

1. JDK系统类库

JRE：Java Runtime Environment（Java运行环境），仅供运行程序的。

JDK：Java Development Kit（Java开发工具包），如果需要进行程序开发，必须安装JDK。

- String、Scanner、包装类...
- java.lang.Thread
- javax.servlet.Servlet : Java扩展的包

2. 第三方类库

- Spring、非Java官方的组织提供的一些成熟好用的工具，C3P0数据库连接池、Spring框架、DBUtils、Dom4J...

github：全球最大同性交友网站

3. 开发者自定义的代码

- 根据具体业务需求编写的业务代码。

### Java中线程的使用 -> Thread是线程Runnable是任务 -> 线程和任务是分不开的

- 继承Thread类

  1. 创建自定义类并继承Thread类

  2. 重写Thread类中的run方法，并编写该线程的业务逻辑代码。

     创建

     ```java
     public class MyThread extends Thread {
       @Override
       public void run() {
         //定义业务逻辑
         for (int i = 0; i < 10; i++) {
           System.out.println("-----MyThread-----");
         }
       }
     }
     ```

     使用

     ```java
     public static void main(String[] args) {
       //开启两个子线程
       MyThread thread1 = new MyThread();
       MyThread2 thread2 = new MyThread2();
       thread1.start();
       thread2.start();
     }
     ```

     注意“不能通过run方法来调用线程的任务，因为run方法调用相当于普通对象的执行，并不会去抢占CPU资源。

     只有通过start方法才能开启线程，进而去抢占CPU资源，当某个线程抢占到CPU资源后，会自动调用run方法。

- 实现Runnable接口：Runnable封装了任务对象，Thread集成线程和对象 ->任务不能单独使用，必须嵌套到线程里

  1. 创建自定义类并实现Runnable接口

  2. 实现run方法，编写该线程的业务逻辑代码

     ```java
     public class MyRunnable implements Runnable {
       @Override
       public void run() {
         //任务
         for (int i = 0; i < 100; i++) {
           System.out.println("=====MyRunnable=====");
         }
       }
     }
     ```

  使用

- 线程和任务：

  线程是去抢占CPU资源的，任务是具体执行业务逻辑的，线程内部会包含一个任务，线程启动（start），当抢占到资源之后，任务就开始执行（run）。

继承Thread替换了run方法；实现Runnable替换了任务

两种方式的区别：

1. 继承Thread类的方式，直接在类中重写run方法，使用的时候，直接实例化MyThread，start即可，因为Thread内部存在Runnable。
2. MyRunnable，实现Runnable接口的方法，在实现类中重写run方法，使用的时候，需要先创建Thread对象，并将MyRunnable注入到Thread中，Thread.start。

实际开发中推荐使用第二种方式。

> 解耦合就是把东西分开。

### 线程的状态 Runnable不是线程（对象），它是线程的一个任务，Runnable没有状态

线程共有5种状态，在特定的情况下，线程可以在不同的状态之间切换，5种状态如下所示。

1. 初始(NEW)：新创建了一个线程对象，但还没有调用start()方法。
2. 运行(RUNNABLE)：Java线程中将就绪（ready）和运行中（running）两种状态笼统的称为“运行”。
线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）。
3. 阻塞(BLOCKED)：表示线程阻塞于锁。
4. 等待(WAITING)：进入该状态的线程需要等待其他线程做出一些特定动作（通知或中断）。
5. 超时等待(TIMED_WAITING)：该状态不同于WAITING，它可以在指定的时间后自行返回。
6. 终止(TERMINATED)：表示该线程已经执行完毕。

- 创建状态：实例化一个新的线程对象，还未启动。

- 就绪状态：创建好的线程对象调用start()方法完成启动，进入线程池等待抢占CPU资源

- 运行状态：线程对象获取了CPU资源，在一定的时间内执行任务

- 阻塞状态：正在运行的线程暂停执行任务，释放所占用的CPU资源，**并在解除阻塞状态之后也不能直接回到运行状态，而是重新回到就绪状态，等待获取CPU资源。**

- 终止状态：线程运行完毕或者因为异常导致该线程终止运行。

  线程状态之间的转换图

  ![Threading03](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading03.svg)

### Java多线程的实现

- 继承Thread
- 实现Runnable

### 线程调度

- 线程休眠

  让当前的线程暂停执行，从运行状态进入阻塞状态，将CPU资源让给其他线程的调度方式，通过sleep()来实现。

  sleep(long milliseconds)，调用时需要传入休眠时间，单位为毫秒。

  ```java
  public class MyThread extends Thread {
    @Override
    public void run() {
      for (int i = 0; i < 10; i++) {
        if (i == 5) {
          try {
            sleep(2000);
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
        }
        System.out.println(i+"----------MyThread");
      }
    }
  }
  ```

  也可以在类的外部调用sleep方法。在外部调用需要注意，休眠一定要放在启动之前。

  ```java
  public static void main(String[] args) {
    MyThread2 thread2 = new MyThread2();
    thread2.start();
    try {
      thread2.sleep(5000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
  ```

  主线程sleep用Thread.sleep -> sleep方法是静态的。

  如何让主线程休眠？直接通过静态方式调用sleep方法。

  ```java
  public class Test2 {
    public static void main(String[] args) {
      for (int i = 0; i < 10; i++) {
        if (i == 5) {
          try {
            Thread.sleep(5000);
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
        }
        System.out.println("+++++test2+++++");
      }
    }
  }
  ```

  sleep是静态的本地方法，可以通过类调用，也可以通过对象调用，方法定义抛出InterrauptedException，InterruptedException继承自Exception，外部调用时必须手动处理异常。try-catch

  ```java
  public static native void sleep(long millis) throws InterruptedException;
  ```

- **线程合并**

  合并是指将指定的某个线程加入到当前线程中，合并为一个线程，由两个线程交替执行完成一个线程中的两个子线程 顺序执行。

  ![Threading04](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading04.svg)

  通过调用join方法来实现合并，具体如何合并？

  线程A和线程B，线程A在执行到某个时间点的时候调用线程B的join方法，则表示从当前时间点开始CPU资源被线程B独占，线程A进入阻塞状态，直到线程B执行完毕，线程A进入就绪状态，等待获取CPU资源进入运行状态。

  join方法重载，join()表示线程B执行完毕之后才能执行其他线程，join(long millis)表示线程B执行millis毫秒之后，无论是否执行完毕，其他线程都可以和它争夺CPU资源。

  ```java
  public class JoinRunnable2 implements Runnable {
    @Override
    public void run() {
      for (int i = 0; i < 20; i++) {
        try {
          Thread.sleep(1000);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        System.out.println(i+"-=-=-=-=-=-=JoinRunnable2");
      }
    }
  }
  ```

  ```java
  public class JoinTest2 {
    public static void main(String[] args) {
      /**
        * 两个线程，主线程、join线程
        * 主线程的逻辑：当i == 10，join线程合并到主线程中
        */
      JoinRunnable2 joinRunnable2 = new JoinRunnable2();
      Thread thread = new Thread(joinRunnable2);
      thread.start();
      for (int i = 0; i < 100; i++) {
        if (i == 10) {
          try {
            thread.join();
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
        }
        System.out.println(i+"=-=-=-=-=-main");
      }
    }
  }
  ```

### 线程礼让

线程礼让是指在某个特定的时间点，让线程暂停抢占CPU资源的行为，运行状态/就绪-->阻塞状态，将CPU资源让给其他线程来使用。

假如线程A和线程B在交替执行，某个时间点线程A做出了礼让，所以在这个时间节点线程B拥有CPU资源，执行业务逻辑，但不代表线程A一直暂停执行。

线程A只是在特定的时间节点礼让，过了时间节点，线程A再次进入就绪状态，和线程B争夺CPU资源。

通过yield方法实现。

```java
public class YieldThread1 extends Thread {
  @Override
  public void run() {
    for (int i = 0; i < 10; i++) {
      if (i == 5) {
        yield();
      }
      System.out.println(Thread.currentThread().getName()+"----------"+i);
    }
  }
}
```

```java
public class YieldThread2 extends Thread {
  @Override
  public void run() {
    for (int i = 0; i < 10; i++) {
      System.out.println(Thread.currentThread().getName()+"=========="+i);
    }
  }
}
```

```java
public class Test {
  public static void main(String[] args) {
    YieldThread1 yieldThread1 = new YieldThread1();
    yieldThread1.setName("线程-1");
    yieldThread1.start();
    YieldThread2 yieldThread2 = new YieldThread2();
    yieldThread2.setName("线程-2");
    yieldThread2.start();
  }
}
```

### 线程中断

> 也是线程礼让的一种方式。

有很多种情况会造成线程停止运行：

- 线程执行完毕会自动停止
- 线程执行过程中遇到错误抛出异常并停止
- 线程在执行过程中根据需求手动停止

Java中实现线程中断有如下几个常用方法：

- public void stop()
- public void interrupt()
- public boolean isInterrupted()

stop方法在新版本中的JDK( >= 1.8)已经不推荐使用，重点关注后面两个方法。

interrupt是一个实例方法，当一个线程对象调用该方法时，表示中断当前线程对象。

每一个线程对象都是通过一个标志位来判断当前是否为中断状态。

isInterrupted就是用来获取当前线程对象的标志位：true表示清除了标志位，当前线程已经中断；false表示没有清除标志位，当前对象没有中断。

当一个线程对象处于不同的状态时，中断机制也是不同的。

创建状态：实例化线程对象，不启动。

```java
public class Test {
  public static void main(String[] args) {
    Thread thread = new Thread();
    System.out.println(thread.getState());
    thread.interrupt();
    System.out.println(thread.isInterrupted());
  }
}
```

```shell
NEW
false
```

NEW表示当前线程对象为创建状态，false表示当前线程并未中断，因为当前线程没有启动，（中断时一个动态的状态），不存在中端，不需要清除标志位。

匿名内部类

```java
public class Test2 {
  public static void main(String[] args) {
    Thread thread = new Thread(new Runnable() {
      @Override
      public void run() {
        for (int i = 0; i < 100; i++) {
          System.out.println(i+"+++++匿名内部类");
        }
      }
    });
    thread.start();
    System.out.println(thread.getState());
    thread.interrupt();
    System.out.println(thread.isInterrupted());
    System.out.println(thread.getState());
  }
}
```

