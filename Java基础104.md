# Math

Math类为开发者提供了一系列的数学方法，同时还提供了两个静态常量E（自然对数的底数）和PI（圆周率）。

```java
public class Test {
  public static void main(String[] args) {
    System.out.println("常量E"+Math.E);
    System.out.println("常量PI"+Math.PI);
    System.out.println("9的平方根"+Math.sqrt(9));
    System.out.println("8的立方根"+Math.cbrt(8));
    System.out.println("2的3次方"+Math.pow(2, 3));
    System.out.println("较大值"+Math.max(6.3, 3.5));
    System.out.println("-10.3的绝对值"+Math.abs(-10.3));
    System.out.println(Math.ceil(10.00001));
    System.out.println(Math.floor(10.9999));
    System.out.println((int) (Math.random()*10));
    System.out.println(Math.rint(5.5)); //四舍五入
  }
}
```

# Random

用来产生随机数的类，并且可以任意指定一个区间，在此区间范围内随机产生一个随机数。

| 方法                         | 描述                                                 |
| ---------------------------- | ---------------------------------------------------- |
| public Random()              | 创建一个无参的随机数构造器，使用系统时间作为默认种子 |
| public Random(long seed)     | 使用long数据类型的种子创建一个随机数构造器           |
| public boolean nextBoolean() | 返回一个布尔类型的随机数                             |
| public double nextDouble()   | 返回一个double类型的随机数,，0.0 - 1.0之间           |
| public float nextFloat()     | 返回一个float类型的随机数，0.0 - 1.0之间             |
| public int nextInt()         | 返回一个int类型的随机数                              |
| public int nextInt(int n)    | 返回一个int类型的随机数， 0 - n之间 [0, n)           |
| public long nextLong()       | 返回一个long类型的随机数，0-1之间                    |

> 生成订单编号（时间戳+随机数）
>
> 1970.1.1 00:00:00-->System.currentTimeMillis()

```java
//开发场景
System.currentTimeMillis()+random.nextInt(100_000)+1_000_000
```

# String

Java通过String类来创建和操作字符串数据。

- String实例化

  1. 直接赋值

     ```java
     String str = "Hello World";
     ```

  2. 通过构造函数创建对象

     ```java
     String str = new String("Hello World");
     ```

     1、2在内存存储方式不一样

![String01](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/String01.svg)

![String02](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/String02.svg)

三目运算符

## String常用方法

| 方法                                                  | 描述                                                         |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| public String()                                       | 创建一个空的字符串对象                                       |
| public String(String value)                           | 创建一个值为value的字符串对象                                |
| public String(char value[])                           | 将一个char类型的数组转换为字符串对象                         |
| public String(char value[], int offset, int count)    | 将一个指定范围的char数组转为字符串对象                       |
| public String(byte value[])                           | 将一个byte类型的数组转换为字符串对象                         |
| public String(byte value[], int offset, int count)    | 将一个指定范围的byte数组转为字符串对象                       |
| public int length()                                   | 获取字符串的长度                                             |
| public boolean isEmpty()                              | 判断字符串是否为空                                           |
| public char charAt(int index)                         | 返回指定下标的字符                                           |
| public byte[] getBytes()                              | 返回字符串对应的bytes数组                                    |
| public boolean equals(Object anObject)                | 判断两个字符串值是否相等                                     |
| public boolean equalsIgnoreCase(Object anObject)      | 判断两个字符串值是否相等（忽略大小写）                       |
| public int compareTo(String value)                    | 对字符串进行排序                                             |
| public int compareToIgnoreCase(String value)          | 忽略大小写进行排序                                           |
| public boolean startsWith(String value)               | 判断字符串是否以value开头                                    |
| public boolean endsWith(String value)                 | 判断字符串是否以value结尾                                    |
| public int hashCode()                                 | 返回字符串的哈希值                                           |
| public int indexOf(String str)                        | 返回str在字符串中的下标                                      |
| public int indexOf(String str, int fromIndex)         | 从指定位置开始查找字符串的下标                               |
| public String subString(int beginIndex)               | 从指定位置开始截取字符串                                     |
| public String subString(int beginIndex, int endIndex) | 截取指定区间的字符串 [beginIndex, endIndex)，左开右闭，取到左边，取不到右边。 |
| public String concat(String str)                      | 追加字符串                                                   |
| public String replaceAll(String o, String n)          | 将字符串中所有的o替换成n                                     |
| public String[] split(String regex)                   | 用指定的字符串对目标进行分割，返回数组                       |
| public String toLowerCase()                           | 转小写                                                       |
| public String toUpperCase()                           | 转大写                                                       |
| public char[] toCharArray()                           | 将字符串转为字符数组                                         |

`null`和`空`是两个概念

- null是指对象不存在，引用地址为空
- 空是指对象存在，没有内容，长度为零

<img src="/Users/anasiangangster/Desktop/Java培训/楠哥/picture/String03.svg" alt="String03" style="zoom:200%;" />

# StringBuffer

String对象一旦创建，值不能修改（原来的值不能修改，一旦修改，就是一个新的对象，只要一改动，就会创建一个新的对象）。

修改之后会重新开辟内存空间来存储新的对象，会修改String的引用。

![String04](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/String04.svg)

String的值为什么不能修改？修改之后会创建一个新的对象？而不是在原有对象的基础上进行修改？

> 因为String底层是数组，数组长度不可变。新的String，就是创建新的数组

因为String底层使用数组来存值的，数组的长度一旦创建就不可修改，所以导致上述问题。

StringBuffer可以解决String频繁修改造成的空间资源浪费的问题。

StringBuffer底层也是使用数组来存值。

StringBuffer底层数组的特点：

- StringBuffer默认的长度为16，使用无参构造函数来创建对象。

  ```java
  //StringBuffer源码
  @HotSpotIntrinsicCandidate
  public StringBuffer() {
    super(16);
  }
  ```

  ```java
  //AbstractStringBuilder源码
  AbstractStringBuilder(int capacity) {
    if (COMPACT_STRINGS) {
      value = new byte[capacity];
      coder = LATIN1;
    } else {
      value = StringUTF16.newBytesFor(capacity);
      coder = UTF16;
    }
  }
  ```

- 使用有参构造创建对象，数组长度=值的长度+16

  ```java
  //StringBuffer源码
  @HotSpotIntrinsicCandidate
  public StringBuffer(String str) {
    super(str);
  }
  ```

  ```java
  //AbstractStringBuilder源码
  AbstractStringBuilder(String str) {
    int length = str.length();
    int capacity = (length < Integer.MAX_VALUE - 16)
      ? length + 16 : Integer.MAX_VALUE;
    final byte initCoder = str.coder();
    coder = initCoder;
    value = (initCoder == LATIN1)
      ? new byte[capacity] : StringUTF16.newBytesFor(capacity);
    append(str);
  }
  ```

  .length()方法返回的并不是底层数组的长度，而是它的有效长度（值的长度）
  
  StringBuffer一旦创建，默认会有16个字节的空间去修改，但是一旦追加的字符串长度超过16，如何处理？
  
  StringBuffer不会重新开辟一块新的内存区域，而是在原有的基础上进行扩容，通过调用父类ensureCapacityInternal()方法对底层数组进行扩容，保持引用不变。

## StringBuffer常用方法

StringBuffer是线程安全的，但是效率较低，StringBuilder是线程不安全的，但效率较高。

HashMap：线程不安全，效率高

Hashtable：线程安全，效率低

| 方法                                                         | 描述                              |
| ------------------------------------------------------------ | --------------------------------- |
| public StringBuffer()                                        | 创建一个空的StringBuffer对象      |
| public StringBuffer(String str)                              | 创建一个值为str的StringBuffer对象 |
| public synchronized int length()                             | 返回StringBuffer值的长度          |
| public synchronized char charAt(int index)                   | 返回指定位置的字符                |
| public synchronized StringBuffer append(String str)          | 追加内容                          |
| public synchronized StringBuffer delete(int start, int end)  | 删除指定区间的值，左开右闭        |
| public synchronized StringBuffer deleteCharAt(int index)     | 删除指定位置的字符                |
| public synchronized StringBuffer replace(int start, int end, String str) | 将指定区间的值替换成str           |
| public synchronized String substring(int start)              | 截取字符串从指定位置到结尾        |
| public synchronized String substring(int start, int end)     | 截取字符串从start开始，到end结束  |
| public synchronized StringBuffer insert(int offset, String str) | 在指定位置插入str                 |
| public int indexOf(String str)                               | 从头开始查找指定字符的位置        |
| public int indexOf(String str, int fromIndex)                | 从fromIndex开始查找指定字符的位置 |
| public synchronized StringBuffer reverse()                   | 进行反转                          |
| public synchronized String toString()                        | 转为String                        |

读取数据不需要考虑线程安全问题，因为这种操作不存在安全隐患。

# 日期类

- java.util.Date

  Data对象表示当前的系统时间

- java.util.Calendar

  Calendar用来完成日期数据的逻辑运算

  运算思路：

  1. 将日期数据传给Calendar（Calendar提供了很大静态常量，专门用来记录日期数据）

| 常量                                 | 描述           |
| ------------------------------------ | -------------- |
| public static final int YEAR         | 年             |
| public static final int MONTH        | 月             |
| public static final int DAY_OF_MONTH | 天，以月为单位 |
| public static final int DAY_OF_YEAR  | 天，以年为单位 |
| public static final int HOUR_OF_DAY  | 小时           |
| public static final int MINIUTE      | 分钟           |
| public static final int SECOND       | 秒             |
| public static final int MILLISECOND  | 毫秒           |

​		2. 调用相关方法进行运算

| 方法                                  | 描述                   |
| ------------------------------------- | ---------------------- |
| public static Calendar getInstance()  | 获取Calendar实例化对象 |
| public void set(int field, int value) | 给静态常量赋值         |
| public int get(int field)             | 获取静态常量的值       |
| public final Date getTime()           | 将Calendar转为Date对象 |

```java
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class Test {
  public static void main(String[] args) {
    Date date = new Date();
    SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    String dateStr = simpleDateFormat.format(date);
    Calendar.getInstance();
    System.out.println(dateStr);
    System.out.println("=============");
    //计算今天所在的周是2021年的第几周
    Calendar calendar = Calendar.getInstance();
    calendar.set(Calendar.YEAR, 2021);
    //1月为0
    calendar.set(Calendar.MONTH, 5);
    calendar.set(Calendar.DAY_OF_MONTH, 11);
    int numOfWeekInYear = calendar.get(Calendar.WEEK_OF_YEAR);
    System.out.println(numOfWeekInYear);
    //今天只后的63天是几月几号
    int days = calendar.get(Calendar.DAY_OF_YEAR);
    System.out.println(days);
    days += 63;
    calendar.set(Calendar.DAY_OF_YEAR, days);
    Date date63 = calendar.getTime();
    SimpleDateFormat simpleDateFormat1 = new SimpleDateFormat("yyyy-MM-dd");
    System.out.println(simpleDateFormat1.format(date63));
    //今天之前的63天是几月几号
    calendar.set(Calendar.DAY_OF_YEAR, 162);
    calendar.set(Calendar.DAY_OF_YEAR, calendar.get(Calendar.DAY_OF_YEAR)-63);
    date = calendar.getTime();
    System.out.println(simpleDateFormat1.format(date));
    System.out.println("==========");
    Calendar calendar1 = Calendar.getInstance();
    System.out.println(calendar1.getTime());
  }
}
```

# File类

Java.io.File，使用该类的构造函数就可以创建文件对象，将硬盘中的一个具体的文件以Java对象的形式来表示。

| 方法                               | 描述                           |
| ---------------------------------- | ------------------------------ |
| public File(String pathname)       | 根据路径创建对象               |
| public String getName()            | 获取文件名                     |
| public String getParent()          | 获取文件所在的目录             |
| public File getParentFile()        | 获取文件所在目录对应的File对象 |
| public String getPath()            | 获取文件路径                   |
| public boolean exists()            | 判断文件是否存在               |
| public boolean isDirectory()       | 判断对象是否为目录             |
| public boolean isFile()            | 判断对象是否为文件             |
| public long length()               | 获取文件的大小                 |
| public boolean createNewFile()     | 根据当前对象创建新文件         |
| public boolean delete()            | 删除对象                       |
| public boolean mkdir()             | 根据当前对象创建目录           |
| public boolean renameTo(File file) | 为已存在的对象重命名           |

# 异常

方法定义时的异常如果直接继承自Exception，实际调用的时候需要手动处理（捕获异常/丢给虚拟机去处理）

方法定义时的异常如果继承自RuntimeException，调用的时候不需要处理。

# 输入输出流IO流

File类

# IO流

- 按照方向分，可以分为输入流和输出流
- 按照单位分，可以分为字节流和字符流
- 按照功能分，可以分为节点流和处理流

## 字节流

按照方向可以分为输入字节流和输出字节流。

InputStream、OutputStream

1 byte = 8 为二进制数

### InputStream常用方法

| 方法                                 | 描述                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| int read()                           | 以字节为单位来读取数据                                       |
| int read(byte b[])                   | 将数据存入byte类型的数组中，并返回数组长度，返回数组中有效数据的长度 |
| int read(byte b[], int off, int len) | 将数据存入byte数组的指定的区间内，返回数据长度               |
| byte[] readAllBytes()                | 将所有数据存入byte数组并返回                                 |
| int available()                      | 返回当前数据流中未读取的数据个数                             |
| void close()                         | 关闭数据流                                                   |

### OutputStream

| 方法                                   | 描述                           |
| -------------------------------------- | ------------------------------ |
| void write(int b)                      | 以字节为单位输出数据           |
| void write(byte b[])                   | 将byte数组中的数据输出         |
| void write(byte b[], int off, int len) | 将byte数组中指定区间的数据输出 |
| void close()                           | 关闭数据流                     |
| void flush()                           | 将缓冲流中的数据同步到输出流中 |
|                                        |                                |

## 字符流

字节流是单位时间内处理一个字节的数据（输入+输出）

字符流是单位时间内处理一个字符的数据（输入+输出） --> JVM 数据

字符流：

- 输入字符流Reader
- 输出字符流Writer

### Reader

```java
//Reader源码
public abstract class Reader implements Readable, Closeable {}
```

是一个抽象类

Readable接口的作用？

可以将数据以字符的形式读入缓冲区

```java
// Reader <--- InputStreamReader <--- FileReader 继承关系
```

- 方向：输入+输出
- 单位：字节+字符
- 功能：节点流（字节流）+处理流（对节点流进行处理，生产其他类型的流）

InputStream（字节输入流），Reader（字符输入流）

InputStreamReader的功能是将字节输入流转换为字符输入流

![IOStream01](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/IOStream01.svg)

英文、数组、符号

1个字符=1个字节

a 1个字符， 1个字节

汉字

1个字符=3个字节

啊 1个字符，3个字节

```java
Reader reader = new FileReader("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo113/test2.txt");
char[] chars = new char[8];
// reader.read()返回字节
int length = reader.read(chars);
System.out.println("数据流的长度是"+length);
System.out.println("遍历数组");
for (char aChar : chars) {
  System.out.println(aChar);
}
```

read()返回的是int，直接将字符转成字节（1-1， 1-3）

read(char[], chars)返回的是char数组，直接就返回字符，不会转成字节的。

```java
import java.io.*;

public class Test {
  public static void main(String[] args) throws IOException {
    Reader reader = new FileReader("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo113/test2.txt");
    char[] chars = new char[8];
    // reader.read()返回字节
    int length = reader.read(chars, 2, 4);
    System.out.println("数据流的长度是"+length);
    System.out.println("遍历数组");
    for (char aChar : chars) {
      System.out.println(aChar);
    }
  }
}
```

### Writer

```java
//Writer源码
public abstract class Writer implements Appendable, Closeable, Flushable {}
```

Appendable接口可以将char类型的数据读入到数据缓冲区

```java
// Writer <--- OutputStreamWriter <--- FileReader 继承关系
```

OutputStreamWriter 处理流

OutputStreamWriter的功能是将输出字节流转成输出字符流，与InputStreamReader向对应的，将输入字节流转成输入字符流。

![IOStream02](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/IOStream02.svg)

```java
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Test {
  public static void main(String[] args) throws IOException {
    Writer writer = new FileWriter("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo118/test.txt");
    //        writer.write("Hello World!你好");
    //        char[] chars = {'你', '好', '世', '界'};
    //        writer.write(chars, 2, 2);
    String str = "HelloWorld, 你好世界";
    writer.write(str, 10, 6);
    writer.flush();
    writer.close();
  }
}
```

## 处理流

```java
import java.io.*;

public class Test2 {
  public static void main(String[] args) throws IOException {
    //基础管道
    InputStream inputStream = new FileInputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo118/test.txt");
    //处理流
    InputStreamReader inputStreamReader = new InputStreamReader(inputStream);
    char[] chars = new char[1024];
    int length = inputStreamReader.read(chars);
    inputStreamReader.close();
    System.out.println(chars);
    String result = new String(chars, 0, length);
    //        result = result.substring(0, length);
    System.out.println(result);
  }
}
```

```java
import java.io.*;

public class Test3 {
  public static void main(String[] args) throws IOException {
    String str = "你好，世界";
    OutputStream outputStream = new FileOutputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo118/test.txt");
    OutputStreamWriter outputStreamWriter = new OutputStreamWriter(outputStream);
    outputStreamWriter.write(str);
    outputStreamWriter.flush();
    outputStreamWriter.close();
  }
}
```

# 缓冲流

```java
// 字节流 --> 字符流 --> 缓冲流  （嵌套）
```

flush

flushable接口

无论是字节流还是字符流，使用的时候都会频繁访问硬盘，对硬盘是一种损伤，同时效率不高，如何解决？

可以使用缓冲流，缓冲流自带缓冲区，可以一次性从硬盘中读取部分数据存入缓冲区，再写入内存，这样就可以有效减少对硬盘的直接访问。

```java
// Java程序 --> 缓冲区 --> 硬盘
```

缓冲流属于处理流，如何来区分节点流（字节流？）和处理流？

- 节点流使用的时候可以直接对接到文件对象File
- 处理流使用的时候可以不直接对接到文件对象File，必须要建立在字节流的基础上才能创建

```java
import java.io.*;

public class Test {
  public static void main(String[] args) throws IOException {
    File file = null;
    //节点流
    InputStream inputStream = new FileInputStream(file);
    OutputStream outputStream = new FileOutputStream(file);

    Reader reader = new FileReader(file);
    Writer writer = new FileWriter(file);

    //处理流
    InputStreamReader inputStreamReader = new InputStreamReader(file); //错
  }
}
```

缓冲流又可以分为字节缓冲流和字符缓冲流，按照方向再细分，又可以分为字节输入缓冲流和字节输入缓冲流，以及字符输入缓冲流和字符输出缓冲流。

```java
import java.io.*;

public class Test4 {
  public static void main(String[] args) throws IOException {
    Reader reader = new FileReader("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo120/test.txt");
    BufferedReader bufferedReader = new BufferedReader(reader);
    char[] chars = new char[1024];
    int length = bufferedReader.read(chars);
    System.out.println(length);
    for (char aChar : chars) {
      System.out.println(aChar);
    }
    bufferedReader.close();
    reader.close();
  }
}
```

## 字节输入缓冲流

```java
import java.io.*;

public class Test {
  public static void main(String[] args) throws IOException {
    //1.创建节点流
    InputStream inputStream = new FileInputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo120/test.txt");
    //2.创建缓冲流
    BufferedInputStream bufferedInputStream = new BufferedInputStream(inputStream);
    //        int temp = 0;
    //        while ((temp = bufferedInputStream.read()) != -1) {
    //            System.out.println(temp);
    //        }

    byte[] bytes = new byte[1024];
    int length = bufferedInputStream.read(bytes, 0, bytes.length);
    for (byte aByte : bytes) {
      System.out.println(aByte);
    }
    bufferedInputStream.close();
    inputStream.close();
  }
}
```

## 字符输入缓冲readline()方法

```java
import java.io.*;

public class Test2 {
  public static void main(String[] args) throws IOException {
    //1.创建字符流（节点流）
    Reader reader = new FileReader("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo120/test.txt");
    //2.创建缓冲流（处理流）
    BufferedReader bufferedReader = new BufferedReader(reader);
    //        bufferedReader.lines().forEach(System.out::println);
    String str = null;
    while ((str = bufferedReader.readLine()) != null) {
      System.out.println(str);
    }
    bufferedReader.close();
    reader.close();
  }
}
```

## 字节输出缓冲流

BufferedOutputStream

```java
import java.io.*;
import java.nio.charset.StandardCharsets;

public class Test {
  public static void main(String[] args) throws IOException {
    OutputStream outputStream = new FileOutputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo121/test.txt");
    BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(outputStream);
    String str = "Spring核心技术详解(一)\n" +
      "一、Sring简介\n" +
      "Spring是一个分层的Java SE/EE应用一站式的轻量级开源框架。Spring核心是IOC和AOP。\n" +
      "Spring主要优点包括：\n" +
      "\n" +
      "方便解耦，简化开发，通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码造成的程序耦合度高。\n" +
      "AOP编程的支持，通过Spring提供的AOP功能，方便进行面向切面编程。\n" +
      "声明式事务的支持，在Spring中，我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。\n" +
      "方便程序的测试，可以用非容器依赖的编程方式进行几乎所有的测试工作。\n" +
      "方便集成各种优秀框架，Spring提供了对各种优秀框架的直接支持。";
    bufferedOutputStream.write(str.getBytes(StandardCharsets.UTF_8));
    bufferedOutputStream.write(str.getBytes(StandardCharsets.UTF_8), 9, 9);
    bufferedOutputStream.flush();
    bufferedOutputStream.close();
    outputStream.close();
  }
}
```

## 字符输出缓冲流

BufferedWriter

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;

public class Test2 {
  public static void main(String[] args) throws IOException {
    Writer writer = new FileWriter("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo121/test2.txt");
    BufferedWriter bufferedWriter = new BufferedWriter(writer);
    String str = "Spring核心技术详解(一)\n" +
      "一、Sring简介\n" +
      "Spring是一个分层的Java SE/EE应用一站式的轻量级开源框架。Spring核心是IOC和AOP。\n" +
      "Spring主要优点包括：\n" +
      "\n" +
      "方便解耦，简化开发，通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码造成的程序耦合度高。\n" +
      "AOP编程的支持，通过Spring提供的AOP功能，方便进行面向切面编程。\n" +
      "声明式事务的支持，在Spring中，我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。\n" +
      "方便程序的测试，可以用非容器依赖的编程方式进行几乎所有的测试工作。\n" +
      "方便集成各种优秀框架，Spring提供了对各种优秀框架的直接支持。";
    bufferedWriter.write(str);
    char[] chars = {'J', 'a', 'v', 'a'}; //字符串底层是字符数组
    bufferedWriter.write(chars);
    bufferedWriter.write(22909); //“好”字
    bufferedWriter.flush();
    bufferedWriter.close();
    writer.close();
  }
}
```

输入流没有flush方法，但不代表没有缓冲流，输出流是有flush方法的，实际开发中在关闭输出缓冲流之前，需要调用flush方法。

# 序列化和反序列化

> 文件读取到内存中以byte、数组、char、String的形式展示。

序列化就是将内存中的对象输出到硬件文件中保存。

反序列化就是相反的操作，从文件中读取数据并还原成内存中的对象。

- 序列化：
  - 对象 --> 序列化 --> 输出到文件中

- 反序列化：
  - 文件 --> 还原成内存中的对象

## 序列化

1. 实体类需要实现序列化接口，Serializable

   ```java
   import java.io.Serializable;
   
   public class User implements Serializable {
     private Integer id;
     private String name;
     private Integer age;
   
     public Integer getId() {
       return id;
     }
   
     public void setId(Integer id) {
       this.id = id;
     }
   
     public String getName() {
       return name;
     }
   
     public void setName(String name) {
       this.name = name;
     }
   
     public Integer getAge() {
       return age;
     }
   
     public void setAge(Integer age) {
       this.age = age;
     }
   
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
   }
   ```

2. 实体类对象进行序列化处理，通过数据流写入到文件中，ObjectOutputStream。

   ```java
   import com.anasiangangster.entity.User;
   
   import java.io.*;
   
   public class Test3 {
     public static void main(String[] args) throws IOException {
       User user = new User(1, "奥博", 25);
       OutputStream outputStream = new FileOutputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo121/test3.txt");
       ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
       objectOutputStream.writeObject(user);
       objectOutputStream.flush();
       objectOutputStream.close();
       outputStream.close();
     }
   }
   ```

## 反序列化

```java
import com.anasiangangster.entity.User;

import java.io.*;

public class Test4 {
  public static void main(String[] args) throws IOException, ClassNotFoundException {
    InputStream inputStream = new FileInputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo121/test3.txt");
    ObjectInputStream objectInputStream = new ObjectInputStream(inputStream);
    User user = (User) objectInputStream.readObject();
    System.out.println(user);
    objectInputStream.close();
    inputStream.close();
  }
}
```

# IO流的应用

IO流就是完成文件传输（上传文件：发朋友圈、换头像；文件下载：CSDN下载源码、文档）

```java
import java.io.*;

public class Test {
  public static void main(String[] args) throws IOException {
    //1.通过输入流将文件读入到Java程序中
    InputStream inputStream = new FileInputStream("/Users/anasiangangster/Desktop/error handling.jpg");
    //2.通过输出流将文件从Java写入到路径中
    OutputStream outputStream = new FileOutputStream("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo122/error handling.jpg");
    int temp = 0;
    while ((temp = inputStream.read()) != -1) {
      outputStream.write(temp);
    }
    outputStream.flush();
    outputStream.close();
    inputStream.close();
  }
}
```

```java
import java.io.*;

//不能读写图，只能用于文本文件（字符文件）
public class Test2 {
  public static void main(String[] args) throws IOException {
    Reader reader = new FileReader("/Users/anasiangangster/Desktop/error handling.jpg");
    Writer writer = new FileWriter("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo122/error handling.jpg");
    int temp = 0;
    while ((temp = reader.read()) != -1) {
      writer.write(temp);
    }
    writer.flush();
    writer.close();
    reader.close();
  }
}
```

```java
import java.io.*;

//不能读写图，只能用于文本文件（字符文件）
public class Test3 {
  public static void main(String[] args) throws IOException {
    Reader reader = new FileReader("/Users/anasiangangster/Desktop/error handling.jpg");
    BufferedReader bufferedReader = new BufferedReader(reader);

    Writer writer = new FileWriter("/Users/anasiangangster/Desktop/Java培训/楠哥/javaBeginner104/src/com/anasiangangster/demo122/error handling.jpg");
    BufferedWriter bufferedWriter = new BufferedWriter(writer);

    String str;
    while ((str = bufferedReader.readLine()) != null) {
      bufferedWriter.write(str);
    }
    bufferedWriter.flush();
    bufferedWriter.close();
    bufferedReader.close();
  }
}
```

