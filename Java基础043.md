# 线程同步

## 并发、并行

使用并发编程的目的？为了充分利用计算机的资源，提高性能，企业以盈利为目的。

并发：多个线程访问同一个共享资源，前提是计算机是单核CPU，多个线程不是同时在访问，而是交替进行，只是因为CPU运行速度太快，看起来是同时在运行。

并行：多核CPU、多个线程是真正的同时在运行，各自占用不同的CPU，相互之间没有影响，也不会争夺资源。

Java默认线程有两个，main（主线程），GC（垃圾回收机制）

synchronized关键字来实现线程同步，让在访问同一个资源的多个线程排队去完成业务，避免出现数据错乱的情况。

double check、volatile

![Threading07](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading07.svg)



### 死锁 Deadlock

前提：一个线程完成业务需要同时访问两个资源。

死锁：多个线程同时在完成业务，出现争抢资源的情况。

```java
public class ChopSticks {
}
```

资源类

```java
public class DeadlockRunnable implements Runnable {
  //编号
  protected int num;
  //资源
  private static ChopSticks chopSticks1 = new ChopSticks();
  private static ChopSticks chopSticks2 = new ChopSticks();

  /**
     * num = 1 拿到chopstick1，等待chopstick2
     * num = 2 拿到chopstick2，等待chopstick1
     */
  @Override
  public void run() {
    if (num == 1) {
      System.out.println(Thread.currentThread().getName()+"拿到了chopstick1，等待获取chopstick2");
      synchronized (chopSticks1) {
        try {
          Thread.sleep(100);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        synchronized (chopSticks2) {
          System.out.println(Thread.currentThread().getName()+"用餐完毕");
        }
      }
    }

    if(num == 2) {
      System.out.println(Thread.currentThread().getName()+"拿到了chopstick2，等待获取chopstick1");
      synchronized (chopSticks2) {
        try {
          Thread.sleep(100);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        synchronized (chopSticks1) {
          System.out.println(Thread.currentThread().getName()+"用餐完毕");
        }
      }
    }
  }
}
```

```java
public class DeadlockTest {
  public static void main(String[] args) {
    DeadlockRunnable deadlockRunnable1 = new DeadlockRunnable();
    deadlockRunnable1.num = 1;
    DeadlockRunnable deadlockRunnable2 = new DeadlockRunnable();
    deadlockRunnable2.num = 2;

    new Thread(deadlockRunnable1, "张三").start();
    new Thread(deadlockRunnable2, "李四").start();
  }
}
```

### 如何破解死锁

不要让多线程并发访问

```java
public class DeadlockTest {
  public static void main(String[] args) {
    DeadlockRunnable deadlockRunnable1 = new DeadlockRunnable();
    deadlockRunnable1.num = 1;
    DeadlockRunnable deadlockRunnable2 = new DeadlockRunnable();
    deadlockRunnable2.num = 2;

    new Thread(deadlockRunnable1, "张三").start();
    //主线程休眠来防止死锁，两个子线程相当于非并发-->单线程
    try {
      Thread.currentThread().sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    new Thread(deadlockRunnable2, "李四").start();
  }
```

### 使用lambda表达式简化代码开发

```java
public class Test3 {
  public static void main(String[] args) {
    //lambda表达式 JDK1.8以上
    //@FunctionalInterface => 函数式接口才支持lambda表达式
    //Thead怎么知道是那个函数式接口呢。
    //Thread类定义：Thread(Runnable target)
    new Thread(() -> {
      //这里就写Runnable抽象方法的实现
      for (int i = 0; i < 100; i++) {
        System.out.println("==========lambda");
      }
    }).start();
  }
}
```

```java
public Thread(Runnable target) {
  this(null, target, "Thread-" + nextThreadNum(), 0);
}
```

```java
new Thread(() -> {for (int i = 0; i < 100; i++) System.out.println("==lambda1");}).start();
new Thread(() -> {for (int i = 0; i < 100; i++) System.out.println("==lambda2");}).start();
```

### Lock

TimeUnit

```java
System.out.println(1);
try {
  Thread.currentThread().sleep(1000);
} catch (InterruptedException e) {
  e.printStackTrace();
}
System.out.println(2);
try {
  TimeUnit.SECONDS.sleep(1);
} catch (InterruptedException e) {
  e.printStackTrace();
}
System.out.println(3);
```

JUC --> java.util.concurrent

Lock是一个接口，用来实现线程同步的，功能与synchronized一样。

Lock使用频率最高的实现类是ReentranLock（重入锁），可以重复上锁。

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Test2 {
  public static void main(String[] args) {
    Account account = new Account();
    new Thread(account, "A").start();
    new Thread(account, "B").start();
  }
}

//不是内部类，非public；public类只能有一个
class Account implements Runnable {
  private static int num;
  private Lock lock = new ReentrantLock();

  @Override
  public void run() {
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    lock.lock();
    num++;
    System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"位访客");
    lock.unlock();
  }
}
```

实现资源和Runnable接口的解耦合。

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Test2 {
  public static void main(String[] args) {
    Account account = new Account();
    //资源和Runnable的解耦合
    new Thread(() -> {
      //真正的业务在这里实现
      account.count();
    }, "A").start();
    new Thread(() -> {
      account.count();
    }, "B").start();
  }
}

//把单纯的资源给分离出来了
class Account {
  private int num;
  private Lock lock = new ReentrantLock();
  public void count() {
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    lock.lock();
    num++;
    System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"位访客");
    lock.unlock();
  }
}
```

## 重入锁

### JUC （勾UC）

java.util.concurrent

Java并发编程工具包，Java官方提供的一套专门用来处理并发编程的工具集合（接口+类）

并发：在单核CPU情况下，多个线程“同时”运行，实际是交替执行，只不过速度太快，看起来是同时执行。

- 两个厨师一口锅

并行：多核CPU，真正的多个线程同时运行。

- 两个厨师两口锅

重入锁是JUC使用频率非常高的一个类ReentrantLock

ReentrantLock就是对synchronized的升级，用类锁资源，目的也是为了实现线程同步。

- ReentrantLock是一个类，synchronized是一个关键字。
- ReentrantLock是JDK实现的，synchronized是虚拟机JVM实现的。
- synchronized可以自动释放锁，ReentrantLock需要手动释放。

ReentranLock是Lock接口的实现类

公平锁和非公平锁的区别

公平锁：线程同步时，多个线程排队，依次执行

非公平锁：线程同步时，可以插队

```java
/**
 * Creates an instance of {@code ReentrantLock}.
 * This is equivalent to using {@code ReentrantLock(false)}.
 */
public ReentrantLock() {
  sync = new NonfairSync();
}

/**
 * Creates an instance of {@code ReentrantLock} with the
 * given fairness policy.
 *
 * @param fair {@code true} if this lock should use a fair ordering policy
 */
public ReentrantLock(boolean fair) {
  sync = fair ? new FairSync() : new NonfairSync();
}
```

## 解耦

- Runnable：是一个需要执行的任务（Runnable）（取快递）

- Thread：执行任务者（张三、A、李四、B）（去执行任务的张三）

- 资源：（Account）（电动车）

线程的实现有两种方式

- 继承Thread类
- 实现Runnable接口

实现Runnable接口的耦合度更低

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Test {
  public static void main(String[] args) {
    Account account = new Account();
    new Thread(() -> {
      account.count();
    }).start();
    new Thread(() -> {
      account.count();
    }).start();
  }
}

/**
 * 将资源和Runnable进行解耦合
 */
class Account {
  private static int num;

  public void count() {
    num++;
    //为什么一加休眠就不对？
    //因为一休眠，线程就需要让出资源
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"访客");
  }
}
```

没有接偶Runnable和资源

```java
import java.util.concurrent.TimeUnit;

public class Test2 {
  public static void main(String[] args) {
    Account2 account2 = new Account2();
    new Thread(account2, "A").start();
    new Thread(account2, "B").start();
  }
}

class Account2 implements Runnable {
  private static int num;

  @Override
  public void run() {
    num++;
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"访客");
  }
}
```

Runnable和资源混在一起

---

```java
class Account {
  private static int num; //Integer
  private Integer id = 0;
  public void count() {
    //锁id可以。锁num不行
    synchronized (Account.class) {
      //num值变了-->另外一个
      num++;
      //为什么一加休眠就不对？
      //因为一休眠，线程就需要让出资源
      try {
        TimeUnit.SECONDS.sleep(1);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      System.out.println(Thread.currentThread().getName() + "是当前的第" + num + "访客");
    }
  }
}
```

如果锁定id则可以同步，锁定num不能同步，原因是什么？

synchronized必须锁定唯一的元素才可以实现同步

num的值每次在变，所以num所指向的引用一直在变，所以不是唯一的元素，肯定无法实现同步。id的值永远不变，所以唯一的元素，可以实现同步。

### ReentrantLock

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Test {
  public static void main(String[] args) {
    Account account = new Account();
    new Thread(() -> {
      account.count();
    }).start();
    new Thread(() -> {
      account.count();
    }).start();
  }
}

class Account {
  private static int num;
  private ReentrantLock lock = new ReentrantLock();
  public void count() {
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    //上锁
    lock.lock();
    num++;
    //解锁
    lock.unlock();
    System.out.println(Thread.currentThread().getName() + "是当前的第" + num + "访客");
  }
}
```

- Lock的上锁和解锁都需要开发者手动完成。
- 可以重复上锁，上几把所就需要解几把所。

ReentrantLock除了可以重入之外，还有一个可以中断的特点，可中断是指某个线程在等待获取锁的过程中可以主动终止线程。

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Test3 {
  public static void main(String[] args) {
    StopLock stopLock = new StopLock();
    Thread thread1 = new Thread(() -> {
      stopLock.service();
    }, "A");
    Thread thread2 = new Thread(() -> {
      stopLock.service();
    }, "B");
    thread1.start();
    thread2.start();
    try {
      TimeUnit.SECONDS.sleep(1);
      thread2.interrupt();
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
  }
}

class StopLock {
  private ReentrantLock lock = new ReentrantLock();

  public void service() {
    try {
      lock.lockInterruptibly();
      System.out.println(Thread.currentThread().getName()+"拿到了锁");
      try {
        TimeUnit.SECONDS.sleep(5);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      lock.unlock();
    }
  }
}
```

---

ReentrantLock限时性：判断某个线程在一定时间内是否能获取锁，通过tryLock方法来实现

tryLock(long time, TimeUnit unit)

time指时间数值

unit时间单位

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Test4 {
  public static void main(String[] args) {
    TimeLock timeLock = new TimeLock();
    /**
         * A拿到锁，执行业务代码，休眠5秒钟
         * B尝试拿锁，需要在3秒中之内拿到锁
         */
    new Thread(()->{
      timeLock.lock();
    }, "A").start();
    new Thread(()->{
      timeLock.lock();
    }, "B").start();
  }
}

class TimeLock {
  private ReentrantLock lock = new ReentrantLock();

  public void lock() {
    /**
         * 尝试在3s内获取锁
         */
    try {
      if (lock.tryLock(3, TimeUnit.SECONDS)) {
        System.out.println(Thread.currentThread().getName()+"拿到锁");
        TimeUnit.SECONDS.sleep(5);
      } else {
        System.out.println(Thread.currentThread().getName()+"没拿到锁");
      }
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      if (lock.isHeldByCurrentThread()) {
        lock.unlock();
      }
    }
  }
}
```

## 生产者消费者模式

在一个生产环境中，生产者和消费者在同一时间段内共享同一快缓冲区，生产者负责向缓冲区添加数据，消费者负责从缓冲区取出数据。

汉堡类

```java
public class Hamburger {
  private int id;

  public int getId() {
    return id;
  }

  public void setId(int id) {
    this.id = id;
  }

  public Hamburger(int id) {
    this.id = id;
  }

  @Override
  public String toString() {
    return "Hamburger{" +
      "id=" + id +
      '}';
  }
}
```

容器类

```java
public class Container {
  public Hamburger[] hamburgers = new Hamburger[6];
  public int index = 0; //下一个空位置的下标

  /**
     * 向容器中添加汉堡
     * 并发操作，不能同时填、同时取
     */
  public synchronized void push(Hamburger hamburger) {
    while (this.index == this.hamburgers.length) {
      //当前线程暂停
      //wait让正在访问当前资源的线程暂停
      try {
        this.wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    this.notify();

    this.hamburgers[index] = hamburger;
    this.index++;
    System.out.println("装了一个汉堡"+hamburger+"@"+(this.index - 1));
  }

  /**
     * 从容器中取出汉堡
     */
  public synchronized Hamburger pop() {
    while (this.index == 0) {
      //当前线程暂停
      //wait让正在访问当前资源的线程暂停
      try {
        this.wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    //唤醒之前暂停的线程
    this.notify();

    this.index--;
    System.out.println("取出一个汉堡"+hamburgers[index]+"@"+this.index);
    return this.hamburgers[index];
  }
}
```

生产者

```java
import java.util.concurrent.TimeUnit;

/**
 * 生产者
 */
public class Producer {
  private Container container;

  public Producer(Container container) {
    this.container = container;
  }

  public void produce() {
    for (int i = 0; i < 30; i++) {
      Hamburger hamburger = new Hamburger(i);
      this.container.push(hamburger);
      try {
        TimeUnit.SECONDS.sleep(1);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }
}
```

消费者

```java
import java.util.concurrent.TimeUnit;

public class Consumer {
  private Container container;

  public Consumer(Container container) {
    this.container = container;
  }

  public void consume() {
    for (int i = 0; i < 30; i++) {
      this.container.pop();
      try {
        TimeUnit.SECONDS.sleep(1);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
  }
}
```

测试类

```java
public class Test {
  public static void main(String[] args) {
    Container container = new Container();
    Producer producer = new Producer(container);
    Consumer consumer = new Consumer(container);
    new Thread(() -> {
      producer.produce();
    }, "生产者线程1").start();
    new Thread(() -> {
      producer.produce();
    }, "生产者线程2").start();
    new Thread(() -> {
      consumer.consume();
    }, "消费者线程1").start();
    new Thread(() -> {
      consumer.consume();
    }, "消费者线程2").start();
  }
}
```

### 多线程并发买票

一场球赛的球票分3个窗口出售，共15张票，用多线程并发来模拟3个窗口的售票情况

```java
import java.util.concurrent.TimeUnit;

public class Ticket {
  //剩余的球票
  private static int surplusCount = 15;
  //已售出的球票
  private static int outCount = 0;

  public synchronized void sale() {
    while(surplusCount > 0) {
      try {
        TimeUnit.MILLISECONDS.sleep(500);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      if (surplusCount == 0) {
        return;
      }
      surplusCount--;
      outCount++;
      if (surplusCount == 0) {
        System.out.println(Thread.currentThread().getName()+"售出"+outCount+"张票，球票已售罄");
      } else {
        System.out.println(Thread.currentThread().getName()+"售出"+outCount+"张票，剩余"+surplusCount+"张票");
      }
    }
  }
}
```

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Ticket {
  //剩余的球票
  private int surplusCount = 15;
  //已售出的球票
  private int outCount = 0;

  private Lock lock = new ReentrantLock();

  public void sale() {
    while (surplusCount > 0) {
      try {
        TimeUnit.MILLISECONDS.sleep(500);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      if (surplusCount == 0) {
        return;
      }
      lock.lock();
      surplusCount--;
      outCount++;
      if (surplusCount == 0) {
        System.out.println(Thread.currentThread().getName() + "售出" + outCount + "张票，球票已售罄");
      } else {
        System.out.println(Thread.currentThread().getName() + "售出" + outCount + "张票，剩余" + surplusCount + "张票");
      }
      lock.unlock();
    }

  }
}
```

```java
public class Test {
  public static void main(String[] args) {
    Ticket ticket = new Ticket();
    new Thread(() -> {
      ticket.sale();
    }, "A").start();
    new Thread(() -> {
      ticket.sale();
    }, "B").start();
    new Thread(() -> {
      ticket.sale();
    }, "C").start();
  }
}
```

