### 线程同步

Java中允许多线程并行访问，同一时间段内多个线程同时完成各自的操作。

多个线程同时操作**同一个共享数据**时，可能会导致数据不准确的问题。

使用线程同步可以解决上述问题。

可以通过synchronized关键字修饰方法实现线程同步，每个Java对象都有一个内置锁，内置锁会保护使用synchronized关键字修饰的方法，要调用该方法就必须先获得锁，否则就处于阻塞状态。

非线程同步

```java
public class Account implements Runnable {

  private static int num;

  @Override
  public void run() {
    //1.num++操作
    //num++不是原子性操作，num+1之后会在下一步将这个值付给nu，所以会出现输出都为1
    num++;
    //2.休眠1毫秒 --> 休眠就会让CPU资源
    try {
      Thread.currentThread().sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    //3.打印输出
    System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"访客");
  }
}
```

```java
public class Test {
  public static void main(String[] args) {
    Account account = new Account();
    for (int i = 0; i < 10; i++) {
      Thread thread = new Thread(account, "线程"+i);
      thread.start();
    }
  }
}
```

线程同步

```java
public class Account implements Runnable {

    private static int num;

    @Override
    public synchronized void run() {
        //1.num++操作
        //num++不是原子性操作，num+1之后会在下一步将这个值付给nu，所以会出现输出都为1
        num++;
        //2.休眠1毫秒 --> 休眠就会让CPU资源
        try {
            Thread.currentThread().sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        //3.打印输出
        System.out.println(Thread.currentThread().getName()+"是当前的第"+num+"访客");
    }
}
```

```java
public class Test {
  public static void main(String[] args) {
    Account account = new Account();
    for (int i = 0; i < 10; i++) {
      Thread thread = new Thread(account, "线程"+i);
      thread.start();
    }
  }
}
```

synchronized关键字可以修饰实例方法，也可以修饰静态方法，两者在使用时是有区别的。

```java
public class SynchronizedTest {
  public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
      Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
          SynchronizedTest.test();
        }
      });
      thread.start();
    }
  }

  /**
     * 先输出start...
     * 间隔1s
     * 输出......end
     * 再输出start...
     */
  public synchronized static void test() {
    System.out.println("start...");
    try {
      Thread.currentThread().sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(".....end");
  }
}

```

synchronized修饰非静态方法

```java
public class SynchronizedTest2 {
  public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
      Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
          SynchronizedTest2 synchronizedTest2 = new SynchronizedTest2();
          synchronizedTest2.test(); //这个方法并没有被所有线程所共享
        }
      });
      thread.start();
    }
  }

  public synchronized void test() {
    System.out.println("start...");
    try {
      Thread.currentThread().sleep(1000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    System.out.println(".....end");
  }
}
```

给实例方法（非静态方法）添加synchronized关键字并不能实现线程同步。

线程同步的本质是锁定多个线程所共享的资源，synchronized还可以修饰代码块，会为代码块加入内置锁，从而实现同步。

```java
public class SynchronizedTest3 {
  public static void main(String[] args) {
    for (int i = 0; i < 5; i++) {
      Thread thread = new Thread(new Runnable() {
        @Override
        public void run() {
          SynchronizedTest3.test();
        }
      });
      thread.start();
    }
  }

  public static void test() {
    //锁类;锁从类来
    synchronized (SynchronizedTest3.class) {
      System.out.println("Start...");
      try {
        Thread.currentThread().sleep(1000);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
      System.out.println(".....end");
    }
  }
}
```

如何判断线程同步或是不同步？

找到关键点：锁定的资源在内存中是一份还是多份？一份大家需要排队，线程同步，多份（一人一份），线程不同步。

无论是锁定方法还是锁定对象，锁定类，只需要分析这个方法、对象、类在内存中有几份即可。

对象一般多份；类一定是一份。

方法就看是静态方法还是非静态方法，静态方法一定是一份，非静态方法一般是多份。

### 线程安全的单例模式

单例模式是一种常见的软件设计模式，核心思想是一个类只一个实例对象。

JVM：栈内存、堆内存；

- 变量：基本数据类型直接存储到栈内存
- 引用类型（通过new创建的对象）：需要栈内存和堆内存结合起来进行存储

> 栈里面只能存数字数值
>
> 通过地址去间接引用，故此叫做引用类型

- SingletonDemo singleton -->开辟栈内存空间，来放地址
- new SingletonDemo() -->开辟堆内存空间

![Threading05](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading05.svg)

单线程模式下的单例模式

```java
public class SingletonDemo {
  private static SingletonDemo singletonDemo;

  private SingletonDemo() {
    System.out.println("创建了SingletonDemo...");
  }

  public static SingletonDemo getInstance() {
    // if (singletonDemo == null) {
    //   singletonDemo = new SingletonDemo();
    //   return singletonDemo;
    // } else {
    //   return singletonDemo;
    // }
    return singletonDemo == null
      ? singletonDemo = new SingletonDemo()
      : singletonDemo;
  }
}

```

```java
public class Test {
  public static void main(String[] args) {
    SingletonDemo singletonDemo = SingletonDemo.getInstance();
    SingletonDemo singletonDemo2 = SingletonDemo.getInstance();
    SingletonDemo singletonDemo3 = SingletonDemo.getInstance();
    System.out.println(singletonDemo.hashCode() == singletonDemo2.hashCode());
    System.out.println(singletonDemo2.hashCode() == singletonDemo3.hashCode());
  }
}
```

线程安全，多线程模式下的单例模式

```java
public class SingletonDemo {
  private static SingletonDemo singletonDemo;

  private SingletonDemo() {
    System.out.println("创建了SingletonDemo...");
  }

  public synchronized static SingletonDemo getInstance() {
    // if (singletonDemo == null) {
    //   singletonDemo = new SingletonDemo();
    //   return singletonDemo;
    // } else {
    //   return singletonDemo;
    // }
    //如果这里有其他业务代码，会变的慢-->需要优化
    //如果这里有其他业务代码，会变的慢-->需要优化
    //如果这里有其他业务代码，会变的慢-->需要优化
    //如果这里有其他业务代码，会变的慢-->需要优化
    return singletonDemo == null
      ? singletonDemo = new SingletonDemo()
      : singletonDemo;
  }
}
```

```java
public class Test2 {
  public static void main(String[] args) {
    //这种写法没用栈内存，直接使用堆内存，直接在堆里面操作，节省了内存空间
    new Thread(new Runnable() {
      @Override
      public void run() {
        SingletonDemo singletonDemo = SingletonDemo.getInstance();
      }
    }).start();

    //这种写法开辟了栈内存
    Thread thread = new Thread(new Runnable() {
      @Override
      public void run() {
        SingletonDemo singletonDemo = SingletonDemo.getInstance();
      }
    });
    thread.start();
  }
}
```

双重检测，synchronized关键字修饰代码块，同步代码块；没必要锁getInstance方法，只要锁核心创建单例的代码就好（没有必要锁整个商场，只需要锁厕所）

- 如果厕所没人-->进去锁住门-->在看一眼确定真的没人-->然后执行

```java
public class SingletonDemo {
  private static SingletonDemo singletonDemo;

  private SingletonDemo() {
    System.out.println("创建了SingletonDemo...");
  }

  public static SingletonDemo getInstance() {
    if (singletonDemo == null) {
      synchronized (SingletonDemo.class) {
        if (singletonDemo == null) {
          singletonDemo = new SingletonDemo();
        }
      }
    }
    return singletonDemo;
  }
}
```

1. 线程同步是为了实现线程安全，如果只创建一个对象，那么线程就是安全的
2. 如果synchronize锁定的是多个线程共享的数据（同一个对象），那么线程就是安全的。

```java
public class SingletonDemo {
  private static SingletonDemo singletonDemo;

  private SingletonDemo() {
    System.out.println("创建了SingletonDemo...");
  }

  public static SingletonDemo getInstance(Integer i) {
    //i创建在这里也可以
    if (singletonDemo == null) {
      //            Integer i = Integer.parseInt("1");
      synchronized (i) {
        if (singletonDemo == null) {
          singletonDemo = new SingletonDemo();
        }
      }
    }
    return singletonDemo;
  }
}
```

```java
public class Test2 {
  public static void main(String[] args) {
    //        User user = new User();
    //这种写法没用栈内存，直接使用堆内存，直接在堆里面操作，节省了内存空间
    new Thread(new Runnable() {
      @Override
      public void run() {
        Integer num1 = Integer.parseInt("1");
        SingletonDemo singletonDemo = SingletonDemo.getInstance(num1);
      }
    }).start();

    //这种写法开辟了栈内存
    Thread thread = new Thread(new Runnable() {
      @Override
      public void run() {
        //如果数字不一样就不是线程安全了，看锁的是不是同一个对象
        //Integer值一样，就是一个对象，Integer的优化。（常量池）
        Integer num2 = Integer.parseInt("1");
        SingletonDemo singletonDemo = SingletonDemo.getInstance(num2);
      }
    });
    thread.start();
  }
}
```

加volatile关键字-->让程序更加严谨-->100%安全了

```java
private volatile static SingletonDemo singletonDemo;
```

volatile关键字的作用可以使内存中的数据对线程可见。

> JMM-->Java内存模型，一个线程在访问内存数据的时候，其实拿到的并不是该数据本身，而是将数据复制并保存在工作内存中的数据。

主内存对线程是不可见的，-->直接访问主内存；添加volatile关键字之后，主内存对线程可见。

```java
//singletonDemo这里是工作内存的数据，并不是主内存的，更新主内存的singletonDemo时锁已经被释放了
singletonDemo = new SingletonDemo();
```

![Threading06](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading06.svg)

