### 安装Java环境

- 编写Java代码

- 编译Java代码，成为字节码文件 16进制

  javac 文件名

-  让JVM执行字节码文件，运行程序

  java 文件名 （不带后缀）

IDE：集成开发环境

Eclipse、IDEA

### 注释

注释就是用通俗易懂的语言对代码进行描述解释，方便自己和他人阅读。

- 单行注释：

  ```java
  //注释内容
  ```

- 多行注释：

  ```java
  /*注释内容
  注释内容
  */
  
  //注释内容
  //注释内容
  //注释内容
  ```

- 文档注释：

  ```java
  /**
   * 注释内容
   * 注释内容
   */
  ```

### 关键字

Java语言预先定义好的，有指定意义的标识符，组成程序的基本元素。

1. abstract 表示抽象
2. boolean 基本数据类型
3. break 跳出循环
4. byte 基本数据类型 字节
5. case 与switch搭配使用 -> 流程控制
6. catch 与太容易搭配使用，表示捕获异常
7. char 基本数据类型
8. class 表示一个类
9. continue 跳出循环
10. do 与while搭配使用，表示循环
11. double 基本数据类型
12. else 与if搭配，流程控制
13. enum 枚举类型
14. extends 继承
15. final 修饰常量
16. float 基本数据类型
17. if 流程控制
18. implements 实现接口
19. import 引用某个类
20. int 基本数据类型
21. interface 表示接口
22. long 基本数据类型
23. native 表示本地方法
24. new 创建对象
25. package 表示包
26. private 私有
27. public 共有
28. return 返回值
29. short 基本数据类型
30. static 表示静态
31. super 表示父类
32. switch 与case搭配使用
33. synchronized 线程同步
34. this 表示当前实例对象
35. throw 抛出异常
36. throws 方法中主动抛出异常
37. try 与catch搭配使用
38. void 表示方法没有返回值
39. volatile 修饰变量，保证线程读取到最新值
40. while 表示循环

### 变量

- 数据类型
- 变量名
- 变量值

### 使用变量

1. 声明变量的数据类型和变量名（包含数据、字母、下划线、$，不能用关键字命名，不能以数字开头），大小写可以混用，首单词应该小写，后续单词的首字母大写。userId 驼峰式命名法。
2. 给内存空间赋值，该值就是变量值。

### Java的数据类型

- 基本数据类型

  byte、**int**、short、long、float、**double**、char、**boolean**

1. 数值类型（整数、小数）

   byte     1B、KB、MB 兆、GB、TB   2^10 ｜一个字节（8位）

   short 两个字节（16位）

   int 四个字节（32位）

   long 八个字节（64位）

   float 四个字节（32位）单精度浮点型

   double 八个字节（64位）双精度浮点型

2. 非数值类型（文本）

   char 字符 两个字节（16位）

   boolean 判断逻辑是否成立 true/falses 八分之一字节（1位）

- 引用数据类型