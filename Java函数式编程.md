# Java函数式编程

## 大纲

- Java 8 Functional Programming基础
  - Functional Programming特点
  - Lambda表达式基本语法
  - 函数式接口
  - 方法引用
- Java 8 Functional Programming进阶
  - Stream基础API
  - filter、map、reduce
  - transducer

## 什么是函数式编程

- 一种编程范式
  - 函数式编程有自己的一套世界观和哲学观
- 函数作为第一对象，三个场景
  - 函数作为变量
  - 函数就可以作为一个参数
  - 返回值本身也可以是个函数
- 注重描述而非具体执行步骤
  - 函数式编程最重要的一个优点
- 更关心代数结构之间的关系
- 不可变

## 函数式编程的编程特性

- 不可变
  - 纯函数式编程是纯不可变的
  - 不用考虑并发问题
  - 对象状态的变化，拆解开了
- 惰性求值
- 闭包
- 高阶函数
- 柯里化
- 部分应用
- 结合率

## Java 8之前早已存在适用函数式编程的场景

- 创建线程
- 策略模式，如comparator
- UI编程
- 异步回调
- 。。。

> 匿名内部类

## Lambda表达式

表达的是方法，表达了一个函数

```java
public static void fn(T param1, R param2){
  // ......
  // ......
}
```

变成

```java
// 没有方法签名，没有类相关的那些东西
XXFunction fn = (T param1, R param2) -> {
  // ......
  // ......
}
```

## Lambda表达式语法糖

- 参数类型可推导
- 单行可省略大括号
- 单参数可省略小括号

```java
(int a, int b) -> { return a + b; }
(a, b) -> a + b
a -> a * a
() -> 1  
```

## 函数式接口SAM(Single Abstract Method)

这是Java为了向前兼容的做法

```java
@FunctionInterface //非必要，如果加上，编译器会进行校验
public Interface Runnable(){ //必须是Inferface
  public abstract void run(); //单个非默认/静态实现方法
}

//后面的Lambda表达式就是Runnable里的run
Runnable r = () -> System.out.println("hello");
```

##  内置常用函数式接口

