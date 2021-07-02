# ForkJoin框架

ForkJoin是JDK1.7后发布的多线程并发处理框架，功能上和JUC类似(也在JUC包里)，JUC更多时候是使用单个类完成操作。ForkJoin使用多个类同时完成某项工作，处理上比JUC更加丰富，实际开发中使用的场景并不是很多，互联网公司真正有高并发需求的情况才会使用。**面试的时候会加分**

本质上是对线程池的一种补充，对线程池功能的一种扩展，基于线程池的。 它的核心思想就是将一个大型的任务拆分成很多个小任务，分别执行，最终将小任务的结果进行汇总，生成最终的结果。

![Threading09](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/Threading09.svg)

本质就是把一个线程的任务拆分成多个小任务，然后由多个线程并发执行，最终将结果进行汇总。

比如A、B两个线程同时执行，A的任务比较多，B的任务相对比较少，B先执行完毕，这时候B去帮助A完成任务（将A的一部分任务拿过来替A执行，执行完毕之后再把结果进行汇总），从而提高效率。

## 工作窃取

ForkJoin框架，核心是两个类

- ForkJoinTask（描述任务）
- ForkJoinPool（线程池）提供多线程并发工作窃取。

使用ForkJoinTask最重要的就是要搞清楚如何拆分任务，这里用的是**递归**思想。

1. 需要创建一个ForkJoinTask任务，ForkJoinTask是一个抽象类,不能直接创建ForkJoinTask的实例化对象，开发者需要自定义一个类，继承ForkJoinTask的子类RecursiveTask，Recursive是递归的意思，该类就提供了实现递归的功能。

   ```java
   import java.util.concurrent.RecursiveTask;
   
   /**
    * 10亿求和, Integer不够大
    */
   public class ForkJoinDemo extends RecursiveTask<Long> {
     private Long start;
     private Long end;
     private Long temp = 1_000_000L; //下划线可以分割
   
     public ForkJoinDemo(Long start, Long end) {
       this.start = start;
       this.end = end;
     }
   
     @Override
     protected Long compute() {
       if ((end - start) < temp) {
         Long sum = 0L;
         for (Long i = start; i <= end; i++) {
           sum += i;
         }
         return sum;
       } else {
         //大于临界值
         Long avg = (start + end) / 2;
         ForkJoinDemo task1 = new ForkJoinDemo(start, avg);
         task1.fork();
         ForkJoinDemo task2 = new ForkJoinDemo(avg, end);
         task2.fork();
         return task1.join()+task2.join();
       }
     }
   }
   ```

   ```java
   import java.util.concurrent.ExecutionException;
   import java.util.concurrent.ForkJoinPool;
   import java.util.concurrent.ForkJoinTask;
   
   public class Test {
     public static void main(String[] args) {
       Long startTime = System.currentTimeMillis();
       ForkJoinPool forkJoinPool = new ForkJoinPool();
       ForkJoinTask<Long> task = new ForkJoinDemo(0L, 1_000_000_000L);
       forkJoinPool.execute(task);
       Long sum = 0L;
       try {
         sum = task.get();
       } catch (InterruptedException e) {
         e.printStackTrace();
       } catch (ExecutionException e) {
         e.printStackTrace();
       }
       Long endTime = System.currentTimeMillis();
       System.out.println(sum+", 共耗时"+(endTime - startTime));
     }
   }
   ```
   
   ```java
   500000511999999744, 共耗时4389
   ```
   
   ```java
   public class Test2 {
     public static void main(String[] args) {
       Long sum = 0L;
       Long startTime = System.currentTimeMillis();
       for (Long i = 0L; i <= 1_000_000_000L; i++) {
         sum += i;
       }
       Long endTime = System.currentTimeMillis();
       System.out.println(sum+"， 共耗时"+(endTime - startTime));
     }
   }
   ```
   
   ```java
   500000000500000000， 共耗时6291
   ```

## volatile关键字

volatile是JVM提供的轻量级同步机制，**可见性**，主内存对线程可见

跳过工作内存

> 一个线程执行完任务之后，会把变量存回到主线程中，并且从主内存中读取当前最新的值。如果是一个空的任务，则不会重新读取主内存中的值。

```java
import java.util.concurrent.TimeUnit;

public class Test2 {
  //空任务、非volatile不会停
  private static int num = 0;

  public static void main(String[] args) {
    new Thread(() -> {
      while (num == 0) {
        //空任务不会停
        System.out.println("---Thread---");
      }
    }).start();

    try {
      TimeUnit.SECONDS.sleep(1);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }

    num = 1;
    System.out.println(num);
  }
}
```

## 线程池的队列workQueue

一个阻塞队列，用来储存等待执行的任务，常用的阻塞队列有一下几种：

- ArrayBlockingQueue：基于数组的**先进先出**队列，创建时必须指定大小
  - 有寻址公式，查找块
- LinkedBlockingQueue：基于链表的**先进先出**列队，创建时可以不指定大小，默认值是Integer.MAX_VALUE
  - 增删快

- SynchronousQueue：它不会保存提交的任务，而是直接新建一个线程来执行新来的任务。
- PriorityBlockingQueue：具有优先级的阻塞队列。

## 递归

- 二叉树的遍历，深度优先搜索。

什么是递归？

常规的定义：编程语言中，函数func直接或者间接调用函数本身，则该函数称为递归函数。

所有的递归问题都可以用递推公式来表示，所以要用递归解决问题，关键就是先找到递推公式。

```java
f(n) = f(n - 1) + 1;
f(n) = 1;
```

f(n)表示你当前是第几排，f(n - 1)前面一排所在的排数。f(1) = 1表示第一排的人知道自己是第一排。

```java
int f(int n) {
  if (n == 1) return 1;
  return f(n - 1) + 1;
}
```

递归需要满足3要素：

1. 一个父问题可以拆分成若干个子问题，并且若干子问题的结果汇总起来就是父问题的答案。
2. 父问题和子问题，解题思路必须完全一致，只是数据规模不同。
3. 终止条件

问题在不断拆分的同时，一定要在某个节点终止拆分，得到一个明确的答案。

---

## 递归（recursion）算法问题

问题：假设有n个台阶，每次可以跨1个台阶或者两个台阶，请问走完这n个台阶一共有多少中走法。

1. 假设有一个台阶，一共有（1）种走法
2. 假设有两个台阶，一共有（2）种走法
3. 假设有三个台阶，一共有（3）种走法

可以根据第一步的走法进行分类

第一类是第一步走了一个台阶

第二类是第一步走了二个台阶

所以n个台阶的走法就等于先走一个台阶后，n-1g个台阶的走法+先走二个台阶后，n-2个台阶的走法。

```java
f(n) = f(n - 1) + f(n - 2);
f(1) = 1;
f(2) = 2;
```

n = 2, f(2) = f(1) + f(0)，如果终止条件只有一个f(1) = 1, f(2)就无法求解，因为f(0)的值无法确定。把f(2) = 2作为一个终止条件。

终止条件有两个：

1. f(1) = 1
2. f(2) = 2
3. n = 3, f(3) = f(2) + f(1) = 3
4. n = 4, f(4) = f(3) + f(2) = 3 + 2 = 5

递推公式

```java
f(1) = 1;
f(2) = 2;
f(n) = f(n - 1) + f(n - 2);
```

```java
int f(int n) {
  if (n == 1) return 1;
  if (n == 2) return 2;
  return f(n - 1) + f(n - 2);
}
```

```java
public class Test {
  public static void main(String[] args) {
    //        System.out.println(f(10));
    for (int i = 1; i <= 10; i++) {
      System.out.println(f2(i));
    }
  }

  public static int f(int n) {
    if (n == 1) return 1;
    return f(n - 1) + 1;
  }

  public static int f2(int n) {
    if (n == 1) return 1;
    if (n == 2) return 2;
    return f2(n - 1) + f2(n - 2);
  }
}
```

