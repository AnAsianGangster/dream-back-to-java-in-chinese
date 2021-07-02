## Treeset

LinkedHashSet和TreeSet都是存储一组有序且唯一的数据，但是这里的两个有序是有区别的。

LinkedHashSet的有序是指元素的存储顺序和遍历顺序是一致的。

6, 3, 4, 5, 1, 2 --> 6, 3, 4, 5, 1, 2

TreeSet的有序是指集合的内部会自动对所有的元素按照生序进行排列，无论存入的顺序的什么，遍历的时候一定按照生序输出。

```java
import java.util.Iterator;
import java.util.TreeSet;

public class Test {
  public static void main(String[] args) {
    TreeSet treeSet = new TreeSet();
    //        treeSet.add(1);
    //        treeSet.add(3);
    //        treeSet.add(6);
    //        treeSet.add(2);
    //        treeSet.add(5);
    //        treeSet.add(4);
    //        treeSet.add(1);
    //        treeSet.add('b');
    //        treeSet.add('a');
    //        treeSet.add('q');
    //        treeSet.add('s');
    //        treeSet.add('a');
    //根据第一字母排序
    treeSet.add("b11");
    treeSet.add("e22");
    treeSet.add("a33");
    treeSet.add("c44");
    treeSet.add("d55");
    System.out.println("treeSet的长度是"+treeSet.size());
    System.out.println("tree遍历");
    //迭代器
    Iterator iterator = treeSet.iterator(); //没有下标，不能用for
    while (iterator.hasNext()) {
      System.out.println(iterator.next());
    }
    System.out.println(treeSet);
  }
}
```

自定义数据类型

```java
import java.util.TreeSet;

public class Test2 {
  public static void main(String[] args) {
    TreeSet treeSet = new TreeSet();
    treeSet.add(new Data(1));
    treeSet.add(new Data(3));
    treeSet.add(new Data(6));
    treeSet.add(new Data(2));
    treeSet.add(new Data(5));
    treeSet.add(new Data(4));
    treeSet.add(new Data(1));
    System.out.println(treeSet.size());
    System.out.println(treeSet); //用Iterator
  }
}

class Data implements Comparable {
  private int num;

  public Data(int num) {
    this.num = num;
  }

  /**
     * A.compareTo(B)
     * 返回值：
     *  1 表示A大于B
     *  0 表示A等于B
     * -1 表示A小于B
     * @param o
     * @return
     */
  @Override
  public int compareTo(Object o) {
    if (o instanceof Data) {
      Data data = (Data) o;
      if (this.num > data.num) return 1;
      else if (this.num == data.num) return 0;
      else return -1;
    }
    return 0;
  }

  @Override
  public String toString() {
    return "Data{" +
      "num=" + num +
      '}';
  }
}
```

## Map

key-value映射，数据字典

List、Set接口都是Collection，Map接口是与Collection完全独立的另外一个体系。

List & Set VS Map

List & Set & Collection只能操作单个元素，Map可以操作一对元素，因为Map存储结构是key-value映射。

Map接口定义时使用了泛型，并且定义了两个泛型K和V，K表示key，规定键元素的数据类型，V表示value，规定值元素的数据类型。

| 方法                                | 描述                                      |
| ----------------------------------- | ----------------------------------------- |
| int size()                          | 获取集合长度                              |
| boolean isEmpty()                   | 判断集合是否为空                          |
| boolean containsKey(Object key)     | 判断集合中是否存在某个key                 |
| boolean containsValue(Object value) | 判断集合中是否存在某个value               |
| V get(Object key)                   | 取出集合中key对应的value                  |
| V put(K key, V value)               | 向集合中存入一组key-value的元素           |
| V remove(Object key)                | 删除集合中key对应的value                  |
| Void putAll(Map map)                | 向集合中添加另外一个Map                   |
| void clear()                        | 清除集合中所有元素                        |
| Set<K> keySet()                     | 取出集合中所有的key，返回一个Set          |
| Collection<V> values()              | 取出集合中所有的value，返回一个Collection |
| Set<Map,Entry<K, V>> entrySet()     | 将Map以Set形式输出                        |
| int hashCode()                      | 获取集合的散列值                          |
| boolean equals(Object o)            | 比较两个集合是否相等                      |

### Map接口的实现类

- HashMap：存储一组无序，key不可以重复，value可以重复的元素。
- Hashtable：存储一组无序，key不可以重复，value可以重复的元素。
- TreeMap：存储一组有序，key不可以重复，value可以重复的元素，可以按照key进行排序。

HashMap常规使用

```java
import java.util.Collection;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

public class Test {
  public static void main(String[] args) {
    HashMap hashMap = new HashMap();
    hashMap.put("h", "Hellow");
    hashMap.put("w", "World");
    hashMap.put("j", "Java");
    hashMap.put("s", "JavaSE");
    hashMap.put("m", "JavaME");
    hashMap.put("e", "JavaEE");
    System.out.println(hashMap);
    hashMap.remove("e");
    System.out.println(hashMap);
    hashMap.put("m", "Model"); //覆盖/修改操作
    System.out.println(hashMap);
    if (hashMap.containsKey("a")) System.out.println("集合中存在key=a");
    else System.out.println("集合中不存在key=a");
    if (hashMap.containsValue("Java")) System.out.println("集合中存在value=Java");
    else System.out.println("集合中不存在value=Java");
    Set keys = hashMap.keySet();
    System.out.println("集合中的key");
    //        System.out.println(keys);
    Iterator iterator = keys.iterator();
    while (iterator.hasNext()) System.out.println(iterator.next());
    Collection values = hashMap.values();
    for (Object value :
         values) {
      System.out.println(value);
    }
    System.out.println("==========");
    iterator = keys.iterator(); //迭代从新赋值
    while (iterator.hasNext()){
      String key = (String) iterator.next();
      System.out.println(key+"-"+hashMap.get(key));
    }
  }
}
```

### Hashtable

Hashtable用法与HashMap基本一样，它们的区别是，Hashtable是线程安全的，但是性能较低。HashMap是非线程安全的，但是性能比较高。HashMap，方法没有用synchronized修饰，所以是非线程安全的。

```java
/**
 * Hashtable源码
 */
public synchronized V put(K key, V value) {
  // Make sure the value is not null
  if (value == null) {
    throw new NullPointerException();
  }

  // Makes sure the key is not already in the hashtable.
  Entry<?,?> tab[] = table;
  int hash = key.hashCode();
  int index = (hash & 0x7FFFFFFF) % tab.length;
  @SuppressWarnings("unchecked")
  Entry<K,V> entry = (Entry<K,V>)tab[index];
  for(; entry != null ; entry = entry.next) {
    if ((entry.hash == hash) && entry.key.equals(key)) {
      V old = entry.value;
      entry.value = value;
      return old;
    }
  }

  addEntry(hash, key, value, index);
  return null;
}
```

Hashtable，方法用synchronized修饰，所以是线程安全的。

Hashtable的使用

```java
import java.util.Collection;
import java.util.Hashtable;
import java.util.Set;

public class Test {
  public static void main(String[] args) {
    Hashtable hashtable = new Hashtable();
    hashtable.put("h", "Hello");
    hashtable.put("w", "World");
    hashtable.put("j", "Java");
    hashtable.put("s", "JavaSE");
    hashtable.put("m", "JavaME");
    hashtable.put("e", "JavaEE");
    System.out.println(hashtable);
    hashtable.remove("e");
    System.out.println(hashtable);
    System.out.println(hashtable.containsKey("a"));
    System.out.println(hashtable.containsValue("JavaME"));
    Set keys = hashtable.keySet();
    System.out.println(keys);
    Collection values = hashtable.values();
    System.out.println(values);
  }
}
```

### HashMap和Hashtable

HashMap和Hashtable，保存的数据都是无序的。Map的另外一个实现类TreeMao主要功能是按照key对集合中的元素进行排序。

## TreeMap

```java
import java.util.Iterator;
import java.util.Set;
import java.util.TreeMap;

public class Test2 {
  public static void main(String[] args) {
    TreeMap treeMap = new TreeMap();
    treeMap.put(3, "Java");
    treeMap.put(5, "JavaME");
    treeMap.put(1, "Hello");
    treeMap.put(6, "JavaEE");
    treeMap.put(2, "World");
    treeMap.put(4, "JavaSE");
    System.out.println(treeMap);
    Set<Integer> keys = treeMap.keySet();
    Iterator<Integer> iterator = keys.iterator();
    while (iterator.hasNext()){
      Integer key = iterator.next();
      System.out.println(
        key + "-" +
        treeMap.get(key)
      );
    }
  }
}
```

```java
import java.util.Iterator;
import java.util.Set;
import java.util.TreeMap;

public class Test3 {
  public static void main(String[] args) {
    TreeMap treeMap = new TreeMap();
    treeMap.put(new User(3, "Java"), "Java");
    treeMap.put(new User(5, "JavaME"), "JavaME");
    treeMap.put(new User(1, "Hello"), "Hello");
    treeMap.put(new User(6, "JavaEE"), "JavaEE");
    treeMap.put(new User(2, "World"), "World");
    treeMap.put(new User(4, "JavaSE"), "JavaSE");
    System.out.println(treeMap);
    Set set = treeMap.keySet();
    Iterator iterator = set.iterator();
    while (iterator.hasNext()) {
      Object key = iterator.next();
      System.out.println(key+"-"+treeMap.get(key));
    }
  }
}

class User implements Comparable {
  private int id;
  private String name;

  public int getId() {
    return id;
  }

  public void setId(int id) {
    this.id = id;
  }

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public User(int id, String name) {
    this.id = id;
    this.name = name;
  }

  @Override
  public String toString() {
    return "User{" +
      "id=" + id +
      ", name='" + name + '\'' +
      '}';
  }

  @Override
  public int compareTo(Object o) {
    if (o instanceof User) {
      User user = (User) o;
      if (this.id > user.id) return 1;
      else if (this.id == user.id) return 0;
      else return -1;
    }
    return 0;
  }
}
```

## Collections工具类

Collection接口，List和Set的父接口

Collections不是接口，它是一个工具类，专门提供了一些对集合的操作，方便开发者去使用，完成相应的业务功能。

Collections针对与集合的工具类。Collection

Arrays针对数组的工具类。Array

| 方法                                                         | 描述                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| public static sort()                                         | 对集合进行排序                                      |
| public static int binarySearch(List list, Object v)          | 查找v在list中的位置，集合必须是生序排列，二分查找法 |
| public static Object get(List list, int index)               | 返回list中index位置对值                             |
| public static void reverse(List list)                        | 对list进行反序输出                                  |
| public static void swap(List list, int i, int j)             | 交换集合中指定位置的两个元素                        |
| public static void fill(List list, Object obj)               | 将集合中所有元素替换成obj                           |
| public static Object min(List list)                          | 返回集合中的最小值                                  |
| public static Object max(List list)                          | 返回集合中的最大值                                  |
| public static boolean replaceAll(List list, Object old, Object new) | 在list集合中用new替换old                            |
| Public static boolean addAll(List list, Object... obj)       | 向集合中添加元素（"..."可变参数，多个、一个、零个） |

可变参数，在调用方法的时候，参数可以是任意个数，但类型必须匹配。

```java
public static void test(int... args) {

}
```

但是下面这种写法，可以传任意类型，任意数量的参数，多态的一种具体表现形式

```java
public static void test(Object... args) {
  
}
```

java中默认输出对象的格式：对象所属的全类名（全限定类名）带着包名的类名+@+对象的哈希值

断点breakpoint

JavaScript，脚本语言，弱语言类型

JavaScript逐行执行，执行一行算一行，假如有10行JavaScript代码，一行一行开始执行，执行到第5行报错，那么后续6-10就不再执行，但是已经执行的前5行结果不变。

Java是必须全部编译之后，统一执行，假如有10行Java代码，必须先对这10行代码进行编译，通过之后，再交给JVM执行。

Java更加严谨，JavaScript更加随意。

Java是强语言类型，JavaScript是弱语言类型。

```java
import java.util.ArrayList;
import java.util.Collections;

public class Test {
  public static void main(String[] args) {
    ArrayList list = new ArrayList();
    //        list.add("Hello");
    //        list.add("World");
    //        Collections.addAll(list, "Java", "JavaSE", "JavaEE", "JavaME");
    //        System.out.println("排序之前"+list);
    //        //进行排序-->生序
    //        Collections.sort(list);
    //        System.out.println("排序之后"+list);
    //        //查找元素在集合中的下标,二分查找法（集合中的元素必须生序排列）
    //        int index = Collections.binarySearch(list, "Hello");
    //        System.out.println("Hello在list中的下标"+index);
    //        //相同直接返回。
    //
    //        Collections.replaceAll(list, "Java", "Collections");
    //        System.out.println(list);
    Collections.addAll(
      list,
      new User(2, "李四", 30),
      new User(1, "张三", 26),
      new User(3, "王五", 18)
    );
    System.out.println(list);
    Collections.sort(list);
    System.out.println(list);
  }
}

class User implements Comparable {
  private Integer id;
  private String name;
  private Integer age;

  public User(Integer id, String name, Integer age) {
    this.id = id;
    this.name = name;
    this.age = age;
  }

  @Override
  public String toString() {
    return "User{" +
      "id=" + id +
      ", name='" + name + '\'' +
      ", age=" + age +
      '}';
  }

  @Override
  public int compareTo(Object o) {
    if (o instanceof User) {
      User user = (User) o;
      if (this.id > user.id) return 1;
      else if (this.id == user.id) return 0;
      else return -1;
    }
    return 0;
  }
}
```

