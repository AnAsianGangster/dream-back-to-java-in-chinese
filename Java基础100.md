### 泛型

泛型（generics），是指在类的定义是不指定类中信息的具体的数据类型，而是暂时用一个表示符来替代，当外部实例化对象的时候再来指定具体的数据类型。

```java
//定义A类的时候就指定类属性是B类型
public class A {
  private B b;
  
  public C test(D d) {
  	return new C();
  }
}
```

```java
//定义A类的时候不指定属性的类型
public class A<T, E, M> {
  private T b;
  
  public E test(M d) {
    return E;
  }
}

A<B, C, D> a = new A();
A<C> a1 = new A();
A<D> a2 = new A();
```

优点：这让做可以极大的提升程序的灵活性，提升类的扩展性，泛型可以指代类中成员变量的数据类型，包括方法的返回值类型以及方法的参数类型。

> 泛型在ArrayList存储数据时，限制入口数据类型，把ArrayList变得像数组一样。

### 泛型的应用

自定义类中添加哦泛型

```java
public class /*类名*/</*泛型1，泛型2，泛型3...*/> {
  private /*泛型1*/ /*属性名*/;
  public /*泛型2*/ /*方法名*/(/*泛型3*/ /*参数名*/) {
    //方法体
  }
}
```

```java
public class TimeFucker<Fuck>{
  private Fuck Fucker;

  public Fuck getFucker() {
    return Fucker;
  }

  public void setFucker(Fuck fucker) {
    Fucker = fucker;
  }
}
```

```java
public class Test {
  public static void main(String[] args) {
    TimeFucker<Integer> time1 = new TimeFucker<>();
    time1.setFucker(10);
    System.out.println("现在的时间是："+time1.getFucker());
    TimeFucker<String> time2 = new TimeFucker<>();
    time2.setFucker("十点整");
    System.out.println("现在的时间是："+time2.getFucker());
  }
}
```

泛型用那个字母都可以，关键是类定义处的字母和类中信息的字母一致。

```java
public class Time<H, M, S> {
    private H hour;
    private M minute;
    private S second;

    public H getHour() {
        return hour;
    }

    public void setHour(H hour) {
        this.hour = hour;
    }

    public M getMinute() {
        return minute;
    }

    public void setMinute(M minute) {
        this.minute = minute;
    }

    public S getSecond() {
        return second;
    }

    public void setSecond(S second) {
        this.second = second;
    }
}
```

```java
public class Test {
  public static void main(String[] args) {
    Time<String, Integer, Float> time = new Time<>();
    time.setHour("十点");
    time.setMinute(10);
    time.setSecond(10.0F);
    System.out.println("现在的时间是"+time.getHour()+":"+ time.getMinute()+":"+ time.getSecond());
  }
}
```

### 泛型通配符

有一个参数为ArrayList的方法，希望这个方法既可以接收泛型是String的集合，又可以接收泛型是Integer的集合，怎么实现。

多态在泛型中不适用

```java
import java.util.ArrayList;

public class Test {
  public static void main(String[] args) {
    ArrayList<String> list1 = new ArrayList<>();
    ArrayList<Integer> list2 = new ArrayList<>();
    test(list1);
    test(list2);
  }

  public static void test(ArrayList<?> list){
    System.out.println(list);
  }
}
```

ArrayList<?>表示可以使用任意的泛型类型对象，这样test方法具备通用性了。

### 泛型的上限和下限

上限：表示实例化时具体的数据类型，可以是上限类型的子类或者是上限类型本身， 用extends表示

- 类名<泛型标识 extends 上限类名>

下限：表示实例化时具体的数据类型，可以是下限类型的父类或者是下限类型本身，用super表示

- 类名<泛型标识 super 下限类名>

```java
public class Time<T> {
  public static void main(String[] args) {
    test(new Time<Float>());
    test(new Time<Integer>());
    test(new Time<Number>());
    test2(new Time<Object>());
    test2(new Time<String>());
    test2(new Time<java.io.Serializable>());
  }

  /**
     * 泛型上限
     * @param time
     */
  public static void test(Time<? extends Number> time) {}

  /**
     * 泛型下限
     * @param time
     */
  public static void test2(Time<? super String> time) {}
}
```

### 泛型接口

接口<?>

```java
public interface MyInterface<T> {
  public T getValue();
}
```

实现泛型接口有两种方式：

```java
public interface MyInterface<T> {
  public T getValue();
}
```

- 实现类在定义时继续使用泛型标识

```java
public class MyInterfaceImpl<T> implements MyInterface<T> {

  private T obj;

  public MyInterfaceImpl(T obj) {
    this.obj = obj;
  }

  @Override
  public T getValue() {
    return this.obj;
  }
}
```

- 实现类在定义时直接给出具体的数据类型

```java
public class MyInterfaceImpl2 implements MyInterface<String> {
  private String obj;

  public MyInterfaceImpl2(String obj) {
    this.obj = obj;
  }

  @Override
  public String getValue() {
    return this.obj;
  }
}
```

使用

```java
public class test {
  public static void main(String[] args) {
    MyInterfaceImpl myInterface = new MyInterfaceImpl<String>("接口");
    String val = (String) myInterface.getValue();

    MyInterfaceImpl2 myInterfaceImpl2 = new MyInterfaceImpl2("接口");
    val = myInterfaceImpl2.getValue();
  }
}
```

### Java实用类

Arrays、Collections、String

- 枚举

> 实例化对象的数量是有限的

枚举Enum，是一种有确定值区间的数据类型，本质上就是一个类，具有简洁、安全、方便等特点。

枚举的值被约束到了一个特定的范围内，只能从这个范围内取值。

为什么要有枚举？

因为在描述某些对象的属性时，该属性的值不能随便定义，必须在某个特定的区间内取值。

> 星期一、星期二...
>
> 性别：男、女
>
> 季节：春、夏、秋、冬

出于对数据的安全性考虑，类似这种有特定去值范围的数据，我们可以使用枚举来描述。

枚举是值一组常量组成的类型，指定一个取值的区间，我们只能从该区间中取值。

```java
//大概语法，编译期，编译成
final class Week extends Enum {
  public static final Week MONDAY;
  public static final Week TUESDAY;
  //...
  public static Week $VALUES[];

  static {
    MONDAY = new Week("MONDAY", 0);
  }
}
// =================
public class UnderLyingEnumWeek extends Enum {
  public static final UnderLyingEnumWeek MONDAY;
  private static final UnderLyingEnumWeek $VALUES[];

  static {
    MONDAY = new UnderLyingEnumWeek("MONDAY", 0);
    $VALUES[] = (new UnderLyingEnumWeek[]{
      MONDAY,
    });
  }

  public static UnderLyingEnumWeek[] values() {
    return $VALUES.clone();
  }

  public static UnderLyingEnumWeek valueOf(String s) {
    return Enum.valueOf(s);
  }

  private UnderLyingEnumWeek(String s, int i) {
    super(s, i);
  }
}
```



