### 集合框架

为什么要使用集合框架？

1. 数组的长度是固定的
2. 数组无法同时存储多个不同的数据类型

集合简单理解就是一个长度可以改变，可以保持任意数据类型的动态数组

集合本身是数据结构的基本概念之一，我们这里说的是集合是Java语言对这种数据结果的具体实现

Java中的集合不是由一个类来完成的，而是由一组接口和类构成了一个框架体系。大致可分为3层，最上层一组接口，继而是接口的实现类。

### 接口

Collection：集合框架最基础的接口，最顶层的接口。

List：Collection的子接口、存储有序、不唯一（元素可重复）的对象，最常用的接口

Set：Collection的子接口、存储无需、唯一（元素不可重复）的对象

Map：独立于Collection的另外一个接口，最顶层的接口。与Collection同级。存储一组键值对象，提供键到值的映射。

Iterator：输出集合元素的接口，一般适用于无序集合，单向输出，从前往后输出。

ListIterator：Iterator子接口，可以双向输出集合中的元素。

Enumeration：传统的输出接口，已经被Iterator取代。

SortedSet：Set的子接口，可以对集合中的元素进行排序。

SortedMap：Map的子接口，可以对集合中的元素进行排序。

Queue：队列接口。

Map.Entry：Map的内部接口，描述Map中储存一组键对值元素。

### Collection接口

Collection是集合框架中最基础的父接口，可以存储一组无序，不唯一的对象。

```java
/**
 * @since 1.2
 */
public interface Collection<E> extends Iterable<E>
```

Collection接口可以存储一组无序，不唯一（可重复）的对象，一般不直接使用该接口，也不能被实例化，只是用来提供规范。

Collection是Iterable接口的子接口。

| 方法                              | 解释                                   |
| --------------------------------- | -------------------------------------- |
| int size()                        | 获取集合长度                           |
| boolean isEmpty()                 | 判断集合是否为空                       |
| boolean contains(Object o)        | 判断集合中是否存在某个对象             |
| iterator<E> iterator()            | 实例化Iterator接口，遍历集合           |
| Ojbect[] toArray()                | 将集合转为一个数组                     |
| T[] toArray(T[] a)                | 将集合转为一个指定数据类型的数组       |
| boolean add(E e)                  | 向集合中添加元素                       |
| boolean containsAll(Collection c) | 判断集合中是否存在另一个集合的所有元素 |
| boolean addAll(Collection c)      | 向集合中添加某个集合的所有元素         |
| boolean removeAll(Collection c)   | 从集合中删除某个集合的所有元素         |
| void clear()                      | 清除集合中的所有元素                   |
| int hashCode()                    | 返回集合的哈希值                       |

### Collection子接口

- List：存放有序，不唯一的元素
- Set：存放无序，唯一的元素
- Queue：列队接口

### List接口

```java
/**
 * @since 1.2
 */
public interface List<E> extends Collection<E>
```

| List常用的扩展方法                          |                                          |
| ------------------------------------------- | ---------------------------------------- |
| T get(int index)                            | 通过下标返回集合中对应位置的元素         |
| T set(int index, T element)                 | 在集合中的指定位置存入对象               |
| int indexOf(Object o)                       | 从前向后，查找某个对象在集合中的位置     |
| int lastIndexOf(Object o)                   | 从后向前，查找某个对象在集合中的位置     |
| ListIterator<E> listIterator()              | 实例化ListIterator接口，用来遍历List集合 |
| List<E> subList(int fromIndex, int toIndex) | 通过下标截取List集合                     |

### List接口的实现类

ArrayList --> 读取比较快 --> 业务逻辑中90%是对数据的读取

Java --> DB --> 集合

ArrayList是开发中使用频率最高的List实现类，实现了长度可变的数组，在内存中分配连续的空间，所以读取快，增删慢。

```java
public class Test {
  public static void main(String[] args) {
    ArrayList arrayList = new ArrayList();
    arrayList.add("Hello");
    arrayList.add("World");
    arrayList.add("JavaSE");
    arrayList.add("JavaME");
    arrayList.add("JavaEE");
    System.out.println("list:"+arrayList);
    System.out.println("list长度:"+arrayList.size());
    System.out.println("list是否包含Java:"+arrayList.contains("JavaEE"));
    for (int i = 0; i < arrayList.size(); i++) {
      System.out.println(arrayList.get(i));
    }
    Iterator iterator = arrayList.iterator();
    while(iterator.hasNext()) {
      System.out.println(iterator.next());
    }
    arrayList.remove("Hello");
    arrayList.remove(0);
    System.out.println(arrayList);
    arrayList.add(0, "Spring"); //移动元素效率比较低
    System.out.println(arrayList); //添加
    arrayList.add(0, "Spring Boot");
    System.out.println(arrayList);
    arrayList.set(0, "Spring Cloud"); //替换
    System.out.println(arrayList);
    System.out.println("*****************");
    System.out.println(arrayList.indexOf("Spring"));
    System.out.println(arrayList.subList(1, 3));
  }
}
```

### List接口的实现类

ArrayList：基于数组的实现，非线程安全，效率比较高，所有的方法都没有synchronized修饰

Vector：线程安全，效率低，实现线程安全直接通过synchronized方式修饰来完成

```java
import java.util.Vector;
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        Vector list = new Vector();
        for (int i = 0; i < 10; i++) {
            int finalI = i;
            new Thread(() -> {
                try {
                    TimeUnit.MILLISECONDS.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                list.add(String.valueOf(finalI));
                System.out.println(list);
            }).start();
        }
    }
}
```

Stack：Vector的子类，实现了栈的数据结构，（后劲先出）

- push：入栈方法
- peek：偷看栈顶元素，将栈顶复制一份取出，取完之后栈内数据不变。
- pop：取出栈顶元素，直接取出栈顶元素，取完之后栈内的数据减一。

```java
import java.util.Stack;

public class Test {
  public static void main(String[] args) {
    Stack stack = new Stack();
    stack.push("Hello");
    stack.push("JavaSE");
    stack.push("JavaME");
    stack.push("JavaEE");
    System.out.println(stack);
    for (int i = 0; i < stack.size(); i++) {
      System.out.println(stack.get(i));
    }
    //peek偷看栈顶元素
    System.out.println("栈顶元素"+stack.peek());
    System.out.println(stack);
    //pop取出栈顶元素
    System.out.println(stack.pop());
    System.out.println(stack);
  }
}
```

LinkedList：实现了先进先出的列队，采用链表的形式存储。

ArrayList和LinkedList区别：内存中储存的形式不同，ArrayList采用的数组的方式，LinkedList采用的是链表的形式。

- 都是线程不安全

数组在内存中存储的空间是连续的，读取快，增删慢

- 因为数组在内存中是连续的，所以取数据可以通过寻址公式很快求出目标元素的内存地址，因为内存是连续的，所以新增或者删除元素，必然需要移动数据，而且数组长度越长，需要移动的元素越多，操作就越慢。

链表在内存中存储空间是不连续的，读取慢，增删快

- 链表在内存中是不连续的，没有固定的公式可以使用，要读取只能从第一位开始一直遍历到目标元素，数据规模越大，操作越慢。
- 增删快，因为只需要从新设置目标元素前后两个节点的后置指针即可，与数据规模无关。

![集合](./picture/集合.svg)

```java
import java.util.LinkedList;

public class Test {
  public static void main(String[] args) {
    LinkedList linkedList = new LinkedList();
    linkedList.add("Hello");
    linkedList.add("World");
    linkedList.add("Java");
    System.out.println(linkedList);
    linkedList.offer("JavaSE");
    System.out.println(linkedList);
    linkedList.push("JavaME");
    System.out.println(linkedList);
    linkedList.addFirst("First");
    System.out.println(linkedList);
    linkedList.addLast("Last");
    System.out.println(linkedList);
    System.out.println(linkedList.peek());
    System.out.println(linkedList.peekFirst());
    System.out.println(linkedList.peekLast());
    System.out.println(linkedList.pop());
    System.out.println(linkedList);
    System.out.println(linkedList.poll());
    System.out.println(linkedList);
  }
}
```

LinkedList和Stack都有pop方法，有什么区别和相同点？

pop方法都是取出集合中的第一个元素，但是两者的顺序是相反的，Stack是“后进先出”，所以pop取出的是最后一个元素，LInkedList是“先进先出”，所以pop取出的是第一个元素。

LInkedList实现了Deque接口，而Deque接口是Queue的子接口，Queue就是队列，底层实现了队列的数据结构。

实际开发中，不能直接实例化Queue对象。

Queue的实现类是AbstractQueue，它是一个抽象类，不能直接实例化，开发中需要实现他的子类PriorityQueue。

Queue中添加的数据必须是有顺序的。

```java
import java.util.PriorityQueue;

public class Test {
  public static void main(String[] args) {
    PriorityQueue queue = new PriorityQueue();
    //        queue.add(1);
    //        queue.add(2);
    //        queue.add(3);
    //        System.out.println(queue);
    queue.add(new A(1));
    queue.add(new A(2));
    A a = new A(1);
    A b = new A(2);
    System.out.println(queue);
  }
}

class A implements Comparable {
  private int num;

  public A(int num) {
    this.num = num;
  }

  @Override
  public int compareTo(Object o) {
    A a = (A) o;
    if (this.num == a.num)
      return 0;
    return this.num > a.num ? 1 : -1;
  }

  @Override
  public String toString() {
    return "A{" +
      "num=" + num +
      '}';
  }
}
```

Queue默认给元素进行生序排列，即自然排序

