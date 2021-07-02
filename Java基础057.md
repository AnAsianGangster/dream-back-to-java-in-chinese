# Java并发编程

## Lock

JUC提供的一种锁机制，功能和synchronized类似，是对synchronized的升级，它是一个接口。

```java
/**
 * 源码
 */
public interface Lock {
  //...
}
```

它的常用的实现类是

- ReentrantLock
- synchronized是通过JVM实现锁机制，ReentrantLock是通过JDK实现锁机制。
- synchronized是一个关键字，ReentrantLock是一个类。
- 重入锁：可以给同一个资源添加多把锁。
- synchronized是线程执行完毕之后自动释放锁，ReentrantLock需要手动解锁。

用synchronized实现买票

```java
import java.util.concurrent.TimeUnit;

public class Test {
  public static void main(String[] args) {
    Ticket ticket = new Ticket();
    new Thread(() -> {
      for (int i = 0; i < 40; i++) {
        ticket.sale();
      }
    }, "A").start();
    new Thread(() -> {
      for (int i = 0; i < 40; i++) {
        ticket.sale();
      }
    }, "B").start();
  }
}

class Ticket {
  private Integer saleNum = 0;
  private Integer lastNum = 30;

  public synchronized void sale() {
    if (this.lastNum > 0) {
      this.saleNum++;
      this.lastNum--;
      try {
        TimeUnit.SECONDS.sleep(1);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      System.out.println(Thread.currentThread().getName()+"卖出类第"+this.saleNum+"张票，还剩"+this.lastNum+"张票");
    }
  }
}
```

用Lock买票

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Test2 {
  public static void main(String[] args) {
    Ticket2 ticket2 = new Ticket2();
    new Thread(() -> {
      for (int i = 0; i < 40; i++) {
        ticket2.sale();
      }
    }, "A").start();
    new Thread(() -> {
      for (int i = 0; i < 40; i++) {
        ticket2.sale();
      }
    }, "B").start();
  }
}

class Ticket2 {
  private Integer saleNum = 0;
  private Integer lastNum = 30;
  private Lock lock = new ReentrantLock();

  public void sale() {
    lock.lock();
    if (this.lastNum > 0) {
      this.saleNum++;
      this.lastNum--;
      try {
        TimeUnit.MILLISECONDS.sleep(500);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      System.out.println(Thread.currentThread().getName()+"卖出类第"+this.saleNum+"张票，还剩"+this.lastNum+"张票");
    }
    lock.unlock();
  }
}
```

## synchronized和Lock的区别

1. synchronized自动上锁，自动释放锁；Lock手动上锁，手动释放锁。

2. synchronized无法判断是否获取到了锁；Lock可以判断是否拿到了锁。

3. synchronized拿不到锁就会一直等待；Lock不一定会一直等待。

4. synchronized是Java关键字；Lock是接口。

5. synchronized是非公平锁；Lock可以设置是否为公平锁。

   公平锁：很公平，排队，当锁没有被占用时，当前线程需要判断列队中是否有其他等待线程。

   非公平锁：不公平，插队，当锁没有被占用时，当前线程可以直接占用，而不需要判断当前列队中是否有等待线程。

实际开发中推荐使用Lock的方式。

ReentrantLock具备限时性的特点，可以判断某个线程在一定的时间段内能否获取到锁，使用tryLock方法，返回值是boolean类型，true表示可以获取到锁，false表示无法获取到锁。

---

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class Test {
  public static void main(String[] args) {
    TimedLock timedLock = new TimedLock();
    new Thread(timedLock::getLock, "A").start();
    new Thread(timedLock::getLock, "B").start();
  }
}

class TimedLock {
  private ReentrantLock lock = new ReentrantLock();

  public void getLock() {
    try {
      if (this.lock.tryLock(3, TimeUnit.SECONDS)) {
        System.out.println(Thread.currentThread().getName()+"拿到了锁");
        TimeUnit.SECONDS.sleep(5);
      } else {
        System.out.println(Thread.currentThread().getName()+"拿不到锁");
      }
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      if (lock.isHeldByCurrentThread())
        lock.unlock();
    }
  }
}
```

## 生产者消费者模式

生产者和消费者共享一份内存空间或共享内存区域 --> 线程通信

- sleep处理线程；wait处理资源

synchronized

```java
import java.util.concurrent.TimeUnit;

public class Test {
  public static void main(String[] args) {
    Data data = new Data();
    new Thread(() -> {
      for (int i = 0; i < 30; i++) {
        data.increment();
      }
    }, "A").start();
    new Thread(() -> {
      for (int i = 0; i < 30; i++) {
        data.decrement();
      }
    }, "B").start();
  }
}

class Data {
  private Integer num = 0;

  public synchronized void increment() {
    while (num != 0) {
      try {
        this.wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    try {
      TimeUnit.MILLISECONDS.sleep(300);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    num++;
    this.notify();
    System.out.println(Thread.currentThread().getName()+"生产了"+num);
  }

  public synchronized void decrement() {
    while (num == 0) {
      try {
        this.wait();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    num--;
    this.notify();
    System.out.println(Thread.currentThread().getName()+"消费了"+num);
  }
}
```

必须使用while判断，不能用if，因为if会存在线程虚假唤醒的问题，虚假唤醒就是一些wait方法会在除了notify的其他情况下被唤醒，不是真正的唤醒，使用while完成多重检测，避免这一问题。

使用Lock

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Test {
  public static void main(String[] args) {
    Data data = new Data();
    new Thread(() -> {
      for (int i = 0; i < 30; i++) data.increment();
    }, "A").start();
    new Thread(() -> {
      for (int i = 0; i < 30; i++) data.decrement();
    }, "B").start();
  }
}

class Data {
  private Integer num = 0;

  private Lock lock = new ReentrantLock();
  private Condition condition = lock.newCondition();

  public void increment() {
    lock.lock();
    while (num != 0) {
      try {
        condition.await();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    try {
      TimeUnit.MILLISECONDS.sleep(300);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    num++;
    condition.signal();
    System.out.println(Thread.currentThread().getName()+"生产了"+num);
    lock.unlock();
  }

  public synchronized void decrement() {
    lock.lock();
    while (num == 0) {
      try {
        condition.await();
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
    }
    num--;
    condition.signal();
    System.out.println(Thread.currentThread().getName()+"消费了"+num);
    lock.unlock();
  }
}
```

使用Lock锁，就不能通过wait和notify来暂停线程和唤醒线程，二应该使用Condition和await和signal来暂停和唤醒线程。

## ConcurrentModificationException

并发修改异常（并发访问异常） --> 多个线程同时并发修改某一个数据时

```java
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class Test {
  public static void main(String[] args) {
    //ArrayList线程不安全
    List<String> list = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
      new Thread(() -> {
        try {
          TimeUnit.MILLISECONDS.sleep(1);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        //单独存在不会抛异常
        //-> 同时读和写会抛异常ConncurrentModifficationException
        // ==========
        //写 --> Vector写时锁起来
        list.add("e");
        //读
        System.out.println(list);
        // ==========
      }, String.valueOf(i)).start();
    }
  }
}
```

### 如何解决？使用线程安全的集合

#### Vector

```java
/**
 * Vector源码
 */
public synchronized boolean add(E e) {
  modCount++;
  add(e, elementData, elementCount);
  return true;
}
```

```java
/**
 * ArrayList源码
 */
public boolean add(E e) {
  modCount++;
  add(e, elementData, size);
  return true;
}
```

#### Collections.synchronizedList

工具类

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class Test {
  public static void main(String[] args) {
    //ArrayList线程不安全
    List<String> list = Collections.synchronizedList(new ArrayList<>());
    for (int i = 0; i < 10; i++) {
      new Thread(() -> {
        try {
          TimeUnit.MILLISECONDS.sleep(1);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        //单独存在不会抛异常
        //-> 同时读和写会抛异常ConncurrentModifficationException
        // ==========
        //写
        list.add("e");
        //读
        System.out.println(list);
        // ==========
      }, String.valueOf(i)).start();
    }
  }
}
```

#### JUC: CopyOnWriteArrayList

CopyOnWwrite写时复制，当我们往一个容器添加元素的时候，不是直接给容器添加，而是先将当前容器复制一份，向新的容器中添加数据，添加完成之后，再将原容器的引用指向新的容器。

##### ArrayList

```java
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.concurrent.TimeUnit;

public class Test2 {
  public static void main(String[] args) {
    List<String> list = new CopyOnWriteArrayList<>();
    for (int i = 0; i < 10; i++) {
      new Thread(() -> {
        try {
          TimeUnit.MILLISECONDS.sleep(10);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        list.add("e");
        System.out.println(list);
      }).start();
    }
  }
}
```

读写分离

```java
/**
 * CoypOnWriteArray源码
 */
public boolean add(E element) {
  synchronized (lock) {
    checkForComodification();
    CopyOnWriteArrayList.this.add(offset + size, element);
    expectedArray = getArray();
    size++;
  }
  return true;
}
```

##### Set

```java
import java.util.Set;
import java.util.concurrent.CopyOnWriteArraySet;
import java.util.concurrent.TimeUnit;

public class Test2 {
  public static void main(String[] args) {
    Set<String> set = new CopyOnWriteArraySet<>();
    for (int i = 0; i < 10; i++) {
      final int finalI = i;
      new Thread(() -> {
        try {
          TimeUnit.MILLISECONDS.sleep(10);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        set.add(String.valueOf(finalI)+"a");
        System.out.println(set);
      }).start();
    }
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(set);
  }
}
```

##### Map

```java
import java.util.Map;
import java.util.UUID;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.TimeUnit;

public class Test2 {
  public static void main(String[] args) {
    Map<String, String> map = new ConcurrentHashMap<>();
    for (int i = 0; i < 10; i++) {
      final int finalI = i;
      new Thread(() -> {
        try {
          TimeUnit.MILLISECONDS.sleep(10);
        } catch (InterruptedException e) {
          e.printStackTrace();
        }
        map.put(String.valueOf(finalI), UUID.randomUUID().toString().substring(0, 3));
        System.out.println(map);
      }).start();
    }
    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(map);
  }
}
```

