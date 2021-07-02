Java并发编程

集合框架JavaSE List Set Map

XML解析

IO流

网络编程

反射机制（动态代理）

JavaWEB

HTML、CSS、JS

Servlet、JSP、

---

企业级框架

SSM

Spring Boot

Spring Cloud

Spring Data JPA

Spring Data Redis

Spring Data MongoDB

Spring Security

Shiro

Spring Boot + Vue

---

自己手写框架

# 线程池

池化技术、池化思想

优势：

- 提高线程的利用率
- 提高响应速度
- 便于统一管理线程对象
- 可控制最大并发数

线程池的具体设计思想：

- 核心池的大小
- 线程池最大容量
- 等待队列
- 拒绝策略

线程池启动的时候会按照核心池的数值来创建初始化的线程对象2个，

开始分配任务，如果同时来了多个任务，2个线程对象都被占用来，第3个以及之后的任务进入等待列队，当前有线程完成任务恢复空闲状态的时候，等待列队中的任务获取线程对象。

如果等待列队也占满了，又有新的任务进来，需要去协调，让线程池再创建新的线程对象，但是线程池不可能无限去创建线程对象，一定会有一个最大上限，就是线程池的最大容量。

如果线程池已经达到了最大上限，并且等待队列也占满了，此时如果有新的任务进来，只能选择拒绝，并且需要根据拒绝策略来选择对应的方案。

> ThreadPoolExecutor

直接实例化ThreadPoolExecutor，实现定制化的线程池，而不推荐使用Executors提供的封装好的方法，因为这种方式代码不够灵活，无法实现定制化。

### ThreadPoolExecutor核心参数

一个有7个

- int corePoolSize：核心池的大小（线程数下限）
- int maximumPoolSize：线程池的最大容量
- long keepAliveTime：线程存活时间（在没有任务执行的情况下），必须是线程池中的数量大于corePoolSize，才会生效
- TimeUnit unit：存活时间单位
- BlockingQueue<Runnable> workQueue：等待队列，存储等待执行的任务
- ThreadFactory threadFactory：线程工厂，用来创建线程对象。
- RejectedExecutionHandler handler：拒绝策略
  - AbortPolicy：直接抛出异常
  - DiscardPolicy：放弃任务，不抛出异常
  - DiscardOldesPolicy：尝试与等待队列中最前面的任务去争夺，不抛出异常
  - CallerRunsPolicy：谁调用谁处理

单例-->AbortPolicy

```java
public static ExecutorService newSingleThreadExecutor() {
  return new FinalizableDelegatedExecutorService
    (new ThreadPoolExecutor(1, 1,
                            0L, TimeUnit.MILLISECONDS,
                            new LinkedBlockingQueue<Runnable>()));
}
```

固定-->AbortPolicy

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
  return new ThreadPoolExecutor(nThreads, nThreads,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>());
}
```

缓存-->AbortPolicy

```java
public static ExecutorService newCachedThreadPool() {
  return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                60L, TimeUnit.SECONDS,
                                new SynchronousQueue<Runnable>());
}
```

new ThreadPoolExecutor.AbortPolicy()

```java
java.util.concurrent.RejectedExecutionException: Task com.anasiangangster.demo070.Test$$Lambda$14/0x0000000800b42840@3e3abc88 rejected from java.util.concurrent.ThreadPoolExecutor@6ce253f1[Running, pool size = 3, active threads = 3, queued tasks = 2, completed tasks = 0]
at java.base/java.util.concurrent.ThreadPoolExecutor$AbortPolicy.rejectedExecution(ThreadPoolExecutor.java:2055)
at java.base/java.util.concurrent.ThreadPoolExecutor.reject(ThreadPoolExecutor.java:825)
at java.base/java.util.concurrent.ThreadPoolExecutor.execute(ThreadPoolExecutor.java:1355)
at com.anasiangangster.demo070.Test.main(Test.java:23)
```

new ThreadPoolExecutor.CallerRunsPolicy()

```shell
main办理业务
pool-1-thread-3办理业务
pool-1-thread-3办理业务
pool-1-thread-3办理业务
pool-1-thread-2办理业务
pool-1-thread-1办理业务
```

new ThreadPoolExecutor.DiscardPolicy()

拿不到放弃，不会抛异常

new ThreadPoolExecutor.DiscardOldestPolicy()

拿不到放弃，不会抛异常

new ThreadPoolExecutor()

```java
import java.util.concurrent.*;

public class Test {
  public static void main(String[] args) {
    ExecutorService executorService = null;
    try {
      /**
             * 自己写7大参数，完全指定化
             */
      executorService = new ThreadPoolExecutor(
        2,
        3,
        1L,
        TimeUnit.SECONDS,
        new ArrayBlockingQueue<>(2),
        Executors.defaultThreadFactory(),
        new ThreadPoolExecutor.AbortPolicy()
      );

      for (int i = 0; i < 6; i++) {
        executorService.execute(() -> {
          //可以休眠，这里不影响
          System.out.println(Thread.currentThread().getName()+"办理业务");
        });
      }
    } catch (Exception  e) {
      e.printStackTrace();
    } finally {
      executorService.shutdown();
    }
  }
}
```

## 线程池三大考点

1. Executors工具类的3种实现

   ```java
   //单例线程池
   ExecutorService executorService = Executors.newSingleThreadExecutor();
   //数量固定线程池
   ExecutorService executorService = Executors.newFixedThreadPool(5);
   //缓存线程池,线程池的线程实例数量随机，由电脑配置决定
   ExecutorService executorService = Executors.newCachedThreadPool();
   ```

2. ThreadPoolExecutor7个参数

   ```java
   - int corePoolSize：核心池的大小（线程数下限）
   - int maximumPoolSize：线程池的最大容量
   - long keepAliveTime：线程存活时间（在没有任务执行的情况下），必须是线程池中的数量大于corePoolSize，才会生效
   - TimeUnit unit：存活时间单位
   - BlockingQueue<Runnable> workQueue：等待队列，存储等待执行的任务
   - ThreadFactory threadFactory：线程工厂，用来创建线程对象。
   - RejectedExecutionHandler handler：拒绝策略
   ```

3. 4种拒绝策略

   ```java
   - AbortPolicy：直接抛出异常
   - DiscardPolicy：放弃任务，不抛出异常
   - DiscardOldesPolicy：尝试与等待队列中最前面的任务去争夺，不抛出异常
   - CallerRunsPolicy：谁调用谁处理
   ```

   