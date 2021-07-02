### 面向对象

万物皆对象

面向对象编程思想：将程序模块化的思想。

- 什么是面向对象？

  面向对象的编程思想诞生之前，程序开发采用的是面向过程的结构化编程方式，是一种面向功能划分的软件结构。

  最小的粒度细滑到方法这一层。

  面向过程注重的是每一个步骤，面向对象关注点在于整个事件的模块化结构。

### 类和对象

类和对象的关系

每个对象都有特定的特征：1、属性。2、方法。

属性指的是对象的静态特征；方法是用来描述对象的动态特征。

对象是用来描述客观存在的一个实体，该实体是由一组属性和方法构成。

类是与对象紧密结合的另外一个概念，类是产生对象的模版，所有的对象都是通过类来创建的。

二者的关系：类是对象的抽象化描述，这些对象都具有相同的特征和动作（属性和方法）。

对象是类的具体实例。

Java程序是以类为组织单元，程序运行时的主体是通过类创建的具体对象。

### 定义类

```java
public class /*类名*/ {
  //定义属性，属性名符合驼峰式命名法
  public /*数据类型 属性名*/;
  //定义方法，方法名符合驼峰式命名法
  public /*返回值类型 方法名*/ (/*参数列表：数据类型 参数名*/) {
    //方法体
	}
}
```

Java关于返回值的定义分为两类：有返回值和无返回值，有返回值的方法需要在方法定义时指定返回值的数据类型，并在方法体中用return将结果返回给外部调用者。

如果一个方法不需要进行返回操作，将返回值类型定义为void，

参数列表是指外部在调用该方法时需要传入到方法内部进行运算的数值。

### 构造函数、构造方法、构造器

构造函数是一种特殊的方法，普通方法是用来描述某个动作的，构造方法是用来创造对象的。

- 方法名必须与类名一致
- 不需要定义返回值类型

构造函数可以分为有参构造和无参构造，有参构造是指带参数的构造函数，无参构造是指没有参数的构造函数。

任何一个类都默认自带一个无参构造函数。如果手动在类中定义一个有参构造，则会覆盖默认的无参构造

### this关键字

this用来指代当前类的实例化对象，通过this可以调用当前对象的属性和方法，比如在有参构造中，通过this将外部传入的值赋给当前类的实例化对象。

this除了可以在类中访问属性也可以在类中调用方法，类中的方法可以分为两类：构造方法、普通方法。

用this调用这两类方的语法也不同，调用构造函数的语法是this（参数列表），不能在普通方法中使用this调用构造函数。

用this调用普通方法，this.方法名（参数列表），可以在构造函数中使用，也可以在普通方法中使用。

### 成员变量和局部变量

变量的作用域是指在程序中可以通过变量名来访问该变量的范围，变量的作用域是由变量被声明时所在的位置决定的，

Java中根据不同的作用域可以将变量分为成员变量和局部变量。

局部变量：如果一个变量在方法中声明，则该变量是局部变量。

成员变量：如果一个变量在方法外，类中声明，则该变量是成员变量。

```java
public class HelloWorld {
  int num2 = 2;
  public void test() {
    int num1 = 1;
  }
}
```

1. 成员变量和局部变量的区别在于作用域不同，成员变量的作用域在整个类中，类中的每一个方法都可以访问该变量，局部变量的作用域只在定义该变量的方法中，出了方法体就无法访问。
2. 成员变量和局部变量的初始值也不同，局部变量不会赋初始值、成员变量会赋初始值，具体的值是由成员变量的数据类型确定的。

### 封装

封装是指将类的属性隐藏在内部，外部不能直接访问和修改，通过修改成员变量的可见性，从公有变私有。

对外有公有方法，公有方法可以对值进行检查和报错或赋默认值。

封装的核心思想就是尽可能把属性都隐藏在内部，对外提供方法来访问，我们可以在这些方法中添加逻辑处理来实现过滤，以屏蔽错误数据的赋值。

封装的步骤：

1. 修改属性（成员变量）的访问权限为私有，使得外部不能直接访问。
2. 提供外部可以直接调用的方法。
3. 在该方法中加入对于属性的逻辑控制，避免出现逻辑上的错误。

什么是访问权限？

访问权限是指该属性可以被直接访问的范围，是在属性定义时设定的，访问权限的可选项一共有4种：private、默认（不写）、protected、public ，区别在于作用域范围不同。

### static

表示静态或者全局，可以用来修饰成员变量和成员方法及代码块。

使用static修饰的成员变量和成员方法独立于该类的任何一个实例化对象，访问时不依赖于该类的对象，而是直接通过类去访问，可以理解为被该类的所有实例对象所共用，所以说是全局的。

static还可以修饰代码块，被static修饰的代码块叫做静态代码块。

```java
 static {
   System.out.println(1);
 }
```

静态代码块的特点是只执行一次，什么时候执行？当这个类被加载到内存时执行，不需要开发者手动调用，会自动执行。

被加载到内存中的类叫运行时类，静态代码块就是在加载类的时候执行，一维类只加载一次，所以静态代码块也只执行一次。

### 继承

- 什么是继承

继承是用来描述类之间的关系，即一个类继承（拥有）另外一个类中的属性和方法，被继承的类叫做父类，继承父类的类叫做子类。

```java
public class /*类名*/ extends /*父类名*/ {
}
```

Java中的继承是单继承，也就是说一个子类只能有一个直接父类。

### 子类访问父类

创建一个子类对象的时候，会默认先创建一个父类对象，此时无论是通过有参构造或是无参构造来创建子类对象，都是通过无参构造来创建父类对象的。

```java
public class People {
  private int id;
  private String name;
  private int age;
  private char gender;

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

  public int getAge() {
    return age;
  }

  public void setAge(int age) {
    this.age = age;
  }

  public char getGender() {
    return gender;
  }

  public void setGender(char gender) {
    this.gender = gender;
  }

  public People() {
    System.out.println("通过无参构造创建了People对象");
  }

  public People(int id, String name, int age, char gender) {
    this.id = id;
    this.name = name;
    this.age = age;
    this.gender = gender;
    System.out.println("通过有参构造创建了People对象");
  }
}
```

```java
public class Student extends People {
  private double score;

  public double getScore() {
    return score;
  }

  public void setScore(double score) {
    this.score = score;
  }

  public Student() {
    System.out.println("通过无参构造创建了Student对象");
  }

  public Student(int id, String name, int age, char gender, double score) {
    super(id, name, age, gender);
    this.score = score;
    System.out.println("通过有参构造创建了Student对象");
  }
}
```

可以通过super关键字让子类创建对象时调用父类的有参构造。

```java
public Student(int id, String name, int age, char gender, double score) {
  super(id, name, age, gender);
  this.score = score;
  System.out.println("通过有参构造创建了Student对象");
}
```

子类可以访问父类的构造方法、普通方法、成员变量，都是通过super关键字来完成，具体语法：

```
构造方法：super（参数列表）
普通方法：super.（方法名）
成言变量：super.成员变量名
```

在子类的构造方法中，可以通过super访问父类的构造方法和普通方法。

在子类法普通方法中，只能通过super访问父类的普通方法。

### 子类的访问权限

访问权限修饰符：public、protected、默认修饰符、private

|            | 同一个类中 | 同一个包中 | 不同包中 | 子类（同包） | 子类（不同包） |
| ---------- | ---------- | ---------- | -------- | ------------ | -------------- |
| public     | 可以访问   | 可以访问   | 可以访问 | 可以访问     | 可以访问       |
| protected  | 可以访问   | 可以访问   | 不能访问 | 可以访问     | 可以访问       |
| 默认修饰符 | 可以访问   | 可以访问   | 不能访问 | 可以访问     | 不能访问       |
| private    | 可以访问   | 不能访问   | 不能访问 | 不能访问     | 不能访问       |

包：package，用来管理Java文件，一个项目中不可避免会出现同名的Java类，为了防止产生冲突，可以把同名的Java类分别放入不同的包中。

包的命名规范：包名由小写字母组成，不能以“.“开头或结尾，可以包含数字，但不能以数字开头，使用”.“来分层。

包的命名方式一般采用网络域名的反向输出，如：com.company.test / com.company.entity

### 方法重写

子类在继承父类方法的基础上，对父类方法重新定义并覆盖的操作叫做方法重写。

构造方法不能被重写，方法重写的规则：

1. 父子类方法名相同。
2. 父子类方法的参数列表相同。
3. 子类方法的返回值与父类方法返回值类型相同或者是其子类。（返回值子类）Object -> Integer
4. 子类方法的访问权限不能小于父类。

### 方法重写VS方法重载

位置：方法重写在子类中对父类方法进行重写，方法重载是在同一类中。

方法名：方法重写相同，方法重载相同。

参数列表：方法重写相同，方法重载不同。

返回值：方法重写相同或是其子类，方法重载没有要求。

访问权限：方法重写不能小于父类，方法重载没有要求。

### 多态

一个事物具有多种表现形态，在Java程序中，定义一个方法，在具体的生产环境中根据不同需求呈现不同的业务逻辑。多态的前提是继承

```java
public class Member {
  public void buyBook() {}
}
```

```java
public class OrdinaryMember extends Member {
  @Override
  public void buyBook() {
    System.out.println("普通会员买书打9折");
  }
}
```

```java
public class SuperMember extends Member{
  @Override
  public void buyBook() {
    System.out.println("超级会员买书打6折");
  }
}
```

```java
public class Test {
  public static void main(String[] args) {
    OrdinaryMember ordinaryMember = new OrdinaryMember();
    Cashier cashier = new Cashier();
    SuperMember superMember = new SuperMember();
    cashier.setMember(ordinaryMember);
    cashier.settlement();
    cashier.setMember(superMember);
    cashier.settlement();
  }
}
```

多态的具体使用有两种形式：

1. 定义方法时形参类型为父类，实际调用方法时传入子类类型的参数。

   ```java
   public void settlement(Member member) {
     member.buyBook();
   }
   
   OrdinaryMember ordinaryMember = new OrdinaryMember();
   Cashier cashier = new Cashier();
   cashier.settlement(ordinaryMember);
   ```

   

2. 定义方法时返回值类型为父类，实际调用方法时返回子类对象。

   ```java
   public Member getMember(String name) {
     if (name.equals("ordinary")) {
       return new OrdinaryMember();
     } else {
       return new SuperMember();
     }
   }
   ```

> !!! 以上两种形式的基本原理都是父类引用可以指向子类对象。

[CSDN 向上转型和向下转型](https://blog.csdn.net/u011630575/article/details/80072241?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.baidujs)

### 抽象方法和抽象类

如果一个方法只有方法的声明而没有具体的方法实现，这个方法就叫做抽象方法，Java中的抽象方法需要使用abstract关键字来修饰。

```java
public abstract class Member {
  public abstract void buyBook();
}	
```

一旦类中定义了抽象方法，则该类也必须声明为抽象类，需要在类定义处添加abstract关键字。

抽象类与普通方法的区别是抽象类不能被实例化，抽象方法与普通方法的区别是抽象方法没有方法体。

抽象类中可以没有抽象方法，但是包含了抽象方法的类必须定义为抽象类。即我们可以在抽象类中定义普通方法，但在普通类中不能定义抽象方法。

如果父类是抽象类，一旦子类继承了该抽象父类，则子类必须对父类的抽象方法进行重写，否则程序报错。

```java
public abstract class Member {
  public abstract void buyBook();
}
```

```java
public class OrdinaryMember extends Member {
  @Override
  public void buyBook() {
    System.out.println("普通会员打9折");
  }
}
```

如果子类也是抽象类，则可以不用重写父类的抽象方法。

### 面向对象

三大特征：封装、继承、多态

### Object类

Object是Java官方提供的类，存放在java.lang包中，该类是所有类的直接父类或者间接父类，无论是Java提供的类还是开发者自定义的类，都是Object的直接子类或间接子类，Java中的任何一个类都会继承Object中的public和protected方法。

```java
public class People {
  public void test() throws CloneNotSupportedException, InterruptedException {
    hashCode();
    getClass();
    equals(null);
    clone();
    toString();
    notify();
    notifyAll();
    wait();
  }
}
```

Oject类中经常被子类重写的方法：

1. public String toString（）以字符串的形式返回对象的信息

   Object

   ```java
   public String toString() {
     return getClass().getName() + "@" + Integer.toHexString(hashCode());
   }
   ```

   重写之后

   ```java
   @Override
   public String toString() {
     return "People{" +
       "id=" + this.id +
       ", name='" + this.name + '\'' +
       ", score=" + this.score +
       '}';
   }
   ```

2. public boolean equals（Object obj）判断两个对象是否相等

   Object类

   ```java
   //Object类
   public boolean equals(Object obj) {
     return (this == obj);
   }
   
   @Override
   public boolean equals(Object o) {
     //this == 0 -> 比较内存地址
     return this == o;
   }
   ```

   重写

   ```java
   @Override
   public boolean equals(Object o) {
     People people = (People) o;
     if(this.id.equals(people.id)
        && this.name.equals(people.name)
        && this.score.equals(people.score)) {
       return true;
     } else {
       return false;
     }
   }
   ```

   

3. public native int hashCode（）返回对象的散列码 -> 对象的内存地址结合对象的内部信息得出来的

   Object类

   ```java
   @HotSpotIntrinsicCandidate
   public native int hashCode();
   ```

   重写

   ```java
   @Override
   public int hashCode() {
     return (int) (id*name.hashCode()*score);
   }
   ```

### 包装类

- 什么是包装类？

  包装类是Java提供的一组类，专门用来创建8种基本数据类型对应的对象，一共有8 个包装类，存放在java.lang包中，基本数据类型对应的包装类。

  | byte    | Byte     |
  | ------- | -------- |
  | short   | Short    |
  | int     | Integer  |
  | long    | Long     |
  | float   | Float    |
  | double  | Double   |
  | char    | Charater |
  | boolean | Boolean  |

  包装类结构体系

  ![包装类](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/包装类.svg)

  ### 装箱和拆箱

  装箱和拆箱是包装类的特有名词，装箱是指将基本数据类型转为对应的包装类型，拆箱就是将包装类对象转为对应的基本类型。

  1. 装箱

     public Type（type value）

     每个包装类都提供了一个有参构造函数：public Type(type value) , 用来实例化包装类对象。
     
     ```java
     byte b = 1;
     Byte byt = new Byte(b);
     short s = 2;
     Short shor = new Short(s);
     int i = 3;
     Integer integer = new Integer(i);
     long l = 4;
     Long lon = new Long(l);
     float f = 5.5F;
     Float floa = new Float(f);
     double d = 6.6;
     Double doubl = new Double(d);
     char c = 'G';
     Character character = new Character(c);
     boolean bo = true;
     Boolean bool = new Boolean(bo);
     System.out.println(byt);
     System.out.println(shor);
     System.out.println(integer);
     System.out.println(lon);
     System.out.println(floa);
     System.out.println(doubl);
     System.out.println(character);
     System.out.println(bool);
     ```
  
  2. public Type(String value) / public Type(char value)
  
     每个包装类还有一个重载构造函数，Character类的重载构造函数：public Type（char value），其他包装类的重载构造函数：public Type（String value）
  
     ```java
     Byte byt = new Byte("1");
     Short shor = new Short("2");
     Integer integer = new Integer("3");
     Long lon = new Long("4");
     Float floa = new Float("5.5F");
     Double doubl = new Double("6.6");
     Character character = new Character('G');
     Boolean bool = new Boolean("true");
     System.out.println(byt);
     System.out.println(shor);
     System.out.println(integer);
     System.out.println(lon);
     System.out.println(floa);
     System.out.println(doubl);
     System.out.println(character);
     System.out.println(bool);
     ```
  
     需要注意的是，Boolean类的构造函数中，当参数为“true”时，Boolean值为true，当参数不为“true”，Boolean值为false。

### 包装类

Java官方提供的一组类，这组类的作用是将基本数据类型的数据封装成引用类型。

Byte、Integer、Short、Long、Float、Double、Boolean、Character

装箱与拆箱

装箱是指将基本数据类型转换为包装类对象

拆箱是指将包装类对象转换为基本数据类型

- 装箱

  1. public Type（type value）

  2. public Type（String value）/ public Type（char value）

  3. value Of（type value）静态方法，参数是基本数据类型的数据

     每一个包装类都有一个valueOf（type value）方法

     ```java
     byte b = 1;
     Byte byt = Byte.valueOf(b);
     short s = 2;
     Short shor = Short.valueOf(s);
     int i = 3;
     Integer integer = Integer.valueOf(i);
     long l = 4L;
     Long lon = Long.valueOf(l);
     float f = 5.5F;
     Float floa = Float.valueOf(f);
     double d = 6.6;
     Double doubl = Double.valueOf(d);
     char ch = 'J';
     Character character = Character.valueOf(ch);
     boolean bo = true;
     Boolean bool = Boolean.valueOf(bo);
     ```

  4. valueOf(String value) / valueOf(char value)专门为Character转换使用的，其他的7个包装类都可以使用

     ```java
     Byte byt = Byte.valueOf("1");
     Short sho = Short.valueOf("2");
     Integer integer = Integer.valueOf("3");
     Long lon = Long.valueOf("4");
     Float floa = Float.valueOf("5.5F");
     Double dou = Double.valueOf("6.6");
     Boolean bool = Boolean.valueOf("true");
     Character character = Character.valueOf('G');
     ```

     需要注意的是Boolean.valueOf(String value)方法中，当value为“true”时，Boolean的值为true，否则，Boolean的值为false。

- 拆箱

  1. *Value()

     每个包装类都有一个*Value()方法，通过该方法可以将包装类转为基本数据类型。

     ```java
     Byte byt = Byte.valueOf("1");
     Short sho = Short.valueOf("2");
     Integer integer = Integer.valueOf("3");
     Long lon = Long.valueOf("4");
     Float floa = Float.valueOf("5.5F");
     Double dou = Double.valueOf("6.6");
     Boolean bool = Boolean.valueOf("true");
     Character character = Character.valueOf('G');
     byte b = byt.byteValue();
     short s = sho.shortValue();
     int i = integer.intValue();
     long l = lon.longValue();
     float f = floa.floatValue();
     double d = dou.doubleValue();
     boolean bo = bool.booleanValue();
     char c = character.charValue();
     ```

  2. Parse*(String value)

     除了Character类以外的每一个包装类都有一个静态方法可以将字符串类型转为基本数据类型。

     ```java
     byte b = Byte.parseByte("1");
     short s = Short.parseShort("2");
     int i = Integer.parseInt("3");
     long l = Long.parseLong("4");
     float f = Float.parseFloat("5.5");
     double d = Double.parseDouble("6.6");
     boolean bo = Boolean.parseBoolean("true");
     ```

  3. toString(type value)

     每一个包装类都有该方法，作用是将基本数据类型转为String类型。

     ```java
     byte b = 1;
     String bstr = Byte.toString(b);
     short s = 2;
     String sstr = Short.toString(s);
     int i = 3;
     String istr = Integer.toString(i);
     long l = 4L;
     String lstr = Long.toString(l);
     float f = 5.5F;
     String fstr = Float.toString(f);
     double d = 6.6;
     String dstr = Double.toString(d);
     char c = 'G';
     String cstr = Character.toString(c);
     boolean bo = true;
     String bostr = Boolean.toString(bo);
     ```

### 接口

- 什么是接口？

  接口是由抽象类衍生出来的一个概念，并由此产生了一种编程方式：面向接口编程。

  面向接口编程就是将程序中的业务模块进行分离，以接口的形式去对接不同的业务模块。

  面向接口编程的优点：当用户需求变更时，只需要切换不同的实现类，而不需要修改串联模块的接口，减少对系统的影响。

  - 能够最大限度的实现解耦合，降低程序的耦合性。
  - 使程序易于扩展。
  - 有利于程序的后期维护

- 如何使用接口？

  接口在Java中是独立存在的一种结构，和类相似，我们需要创建一个接口文件，Java中用class关键字来标识类，用interface来标识接口，基本语法：

   ```java
   public interface /*接口名*/ {
     public /*返回值*? /*方法名*/(/*参数列表*/);
   }
   ```

  接口就是一个抽象类，极度抽象的抽象类。

  抽象类：一个类中一旦存在没有具体实现的抽象方法时，那么该类就必须被定义为抽象类，同时抽象类存在非抽象方法。

  但是接口完全不同，接口中不能存在非抽象方法，接口中必须全部都是抽象方法。

  因为接口中必须都是抽象方法，所以修饰抽象方法的关键字abstract可以省略。

  接口中允许定义成员变量，但是有如下要求：

  1. 不能定义private和protected修饰的成员变量，只能定义public和默认访问权限修饰符修饰的成员变量。

  2. 接口中的成员在定义时就必须完成初始化

  3. 接口中的成员变量都是静态常量，即可以直接通过接口访问，同时值不能被修改。

     ```java
     public interface MyInterface {
       public int ID = 0;
       String name = "张三";
     
       public void test();
     }
     ```

     使用接口时，不能直接实例化接口对象，而是必须实例化其实现类对象，实现类本身就是一个普通的Java类，创建实现类的代码如下所示。

     ```java
     public class MyInterfaceImpl implements MyInterface{
       @Override
       public void test() {
     
       }
     }
     ```

     通过implements关键字来指定实现类具体要实现的接口，在实现类的内部需要对接口的所有抽象方法进行实现，同时要求访问权限修饰符、返回值类型、方法名和参数列表必须完全一致。

     接口和继承，Java只支持单继承，但是接口可以多实现(一个实现类可以同时实现多个接口)。

### 面向接口编程的实际应用

面向接口编程是一种常用的编程方式，可以有效地提高代码的复用性，增强程序的扩展性和维护性。

- 场景

某工厂生产产品A，产品A主要是由设备A来完成生产，用程序模拟生产过程。

产品B是由设备B来生产的。

当需求发生改变时，就要频繁修改类的内部结构方法是需要避免的，因为这种结构的程序扩展性很差，如何改进？使用面向接口编程即可。

1. 创建接口Equipment

   ```java
   public interface Equipment {
     public void work();
   }
   ```

2. 创建Equipment的实现类

   ```java
   public class EquipmentA implements Equipment {
     @Override
     public void work() {
       System.out.println("设备A运行，生产产品A");
     }
   }
   ```

3. 创建Factory类

   ```java
   public class Factory {
     //注入    
     private Equipment equipment;
   
     public Equipment getEquipment() {
       return equipment;
     }
   
     public void setEquipment(Equipment equipment) {
       this.equipment = equipment;
     }
   
     public void work() {
       System.out.println("开始生产。。。");
       this.equipment.work();
     }
   }
   ```

4. Test

   ```java
   public class Test {
     public static void main(String[] args) {        
       Equipment equipmentA = new EquipmentA();
       factory.setEquipment(equipmentA);
       factory.work();
     }
   }
   ```

###  异常

- 什么是异常？

  Java中的错误大致可以分为两类：

  - 一类是编译时错误，一般是指语法错误；

  - 另一类是运行时的错误。

Try-catch-finally

```java
try {
	//可能会抛出异常的代码
} catch (Exception e) {
  //对异常进行处理
} finally {
  //一定会执行的代码
}
```

### 异常类

- Error：系统错误，程序无法处理。
- Exception：程序运行时出现的错误，程序可以处理。

Error和Exception都是Throwable的子类，Throwable、Error、Exception都是存放在java.lang包中。

- Error常见的子类：VirtualMachineError、AWTError、IOError

  - VirtualMachineError的子类：StackOverflowError、OutOfMemoryError，用来描述内存溢出等系统问题。

- Exception常见的子类：IOException和RuntimeException

  - IOException存放在java.io包中，RuntimeException存放在java.lang包中。

    - IOException常见的子类：FileLockInterruptionException、FileNotFoundException、FilerException，这些异常通常都是处理通过IO流进行文件传输的时候发生的错误。

  - RuntimeException常见的子类

    - ArithmeticException：表示数学运算异常。

    - ClassNotFoundException：表示未定义异常。

    - IllegalArgumentException：表示参数格式异常。

    - ArrayIndexOutOfBounds：表示数组下标越界。

    - NullPointException：表示空指针异常。

    - NoSuchMethodException：表示方法未定义异常。

    - NumberFormatException：表示将其他数据类型转为数值类型发生的类型不匹配异常。

      ```java
      public class Test {
        public static void main(String[] args) {
          try {
            // int num = 10/0;
            // Class clazz = Class.forName("test.Student");
            // int[] array = {1, 2, 3};
            // System.out.println(array[3]);
            // String str = null;
            // str.charAt(0);
            // Class clazz = Class.forName("com.exceptions.Student");
            // Method method = clazz.getMethod("test2", null);
            // Integer.parseInt("abc");
          } catch (Exception e) {
            e.printStackTrace();
          }
          System.out.println("Hi");
        }
      }
      ```

### 抛出异常的方式

### throw和throws

throw和throws是java在处理异常时使用的两个关键字，都可以用来抛出异常，但是使用的方法和表示的含义完全不同。

Java中抛出异常有3种方式：

- try-catch捕获异常

  捕获后，程序会接着运行

- throw是开发者主动抛出异常，即读到throw代码就一定抛出异常，基本语法：

  ```java
  throw new Exception();
  ```

  是基于代码的逻辑而主动抛出异常的方式。

  ```java
  public static void main(String[] args) {
    int[] array = {1, 2, 3};
    test(array, 2);
    System.out.println("hi");
  }
  
  private static void test(int[] array, int index) {
    try {
      if (index >= array.length || index < 0) {
        throw new Exception();
      } else {
        System.out.println(array[index]);
      }
    } catch (Exception e) {
      e.printStackTrace();
    }
  }
  ```

- try-catch和throw都是作用于具体的逻辑代码，throws是作用于方法的，用来描述方法可能会抛出的异常。

  -> 方法自己不处理，扔给调用的人

  如果方法throws的是RuntimeException异常或其子类，外部调用时可以不处理，JVM会处理。

  如果方法throws的是Exception异常或是其子类，外部调用时必须处理，否则报错。

  ```java
  public static void main(String[] args) {
    try {
      test("abc");
    } catch (NumberFormatException e) {
      e.printStackTrace();
    }
    System.out.println("Hi");
  }
  
  private static void test(String str) throws NumberFormatException {
    int num = Integer.parseInt(str);
  }
  ```


### 异常捕获

- 自动捕获try-catch
- throw主动抛出异常
- throws修饰可能抛出异常的方法

### 自定义异常

除了使用Java提供的异常之外，也可以根据需求来自定义异常。

```java
public class MyNumberException extends RuntimeException {
  public MyNumberException(String error) {
    super(error);
  }
}
```

```java
public class Test {
  public static void main(String[] args) {        
    Test test = new Test();
    System.out.println(test.add("a"));
    System.out.println("Hi");
  }    

  private int add(Object object) {
    if (object instanceof Integer) {
      int num = (int) object;
      return ++num;
    } else {
      String error = "传入的参数不是整数类型";
      MyNumberException myNumberException = new MyNumberException(error);
      throw myNumberException;
    }
  }
}
```

### 综合练习

封装、继承、多态、抽象、接口、异常完成一个汽车查询系统。

需求描述：共有3种类型的汽车：小轿车、大巴车、卡车，其中小轿车的座位数是四座，大巴车座位数是五座，卡车座位数是两座。要求使用封装、继承、抽象来完成车辆的定义。

可以对车辆信息进行修改，卡车可以运货但是载重量不能超过12吨，使用自定义异常来处理错误，小轿车和大巴车没有此功能，要求使用接口来实现。

抽象类

```java
public abstract class Car {
  private String name;
  private String color;

  public String getName() {
    return name;
  }

  public void setName(String name) {
    this.name = name;
  }

  public String getColor() {
    return color;
  }

  public void setColor(String color) {
    this.color = color;
  }

  public Car(String name, String color) {
    this.name = name;
    this.color = color;
  }

  public abstract String seatNum();
}
```

接口

```java
public interface Container {
  public int getWeight();
}
```

自定义异常

```java
public class CarException extends Exception {
  public CarException(String error) {
    super(error);
  }
}
```

轿车

```java
public class Sedan extends Car {
  public Sedan(String name, String color) {
    super(name, color);
  }

  @Override
  public String seatNum() {
    return "四座";
  }
}
```

巴士

```java
public class Bus extends Car {
  public Bus(String name, String color) {
    super(name, color);
  }

  @Override
  public String seatNum() {
    return "五十三座";
  }
}
```

卡车

```java
public class Truck extends Car implements Container {

  private int weight;

  public Truck(String name, String color, int weight) {
    super(name, color);
    this.weight = weight;
  }

  @Override
  public String seatNum() {
    return "两座";
  }

  @Override
  public int getWeight() {
    return this.weight;
  }
}
```

Test

```java
import java.util.Scanner;

public class Test {
  private static Scanner scanner;
  private static Sedan sedan;
  private static Bus bus;
  private static Truck truck;
  private static Car[] cars;

  static {
    scanner = new Scanner(System.in);
    sedan = new Sedan("小轿车", "黑色");
    bus = new Bus("大巴车", "绿色");
    truck = new Truck("卡车", "蓝色", 2);
    cars = new Car[3];
    cars[0] = sedan;
    cars[1] = bus;
    cars[2] = truck;
  }

  public void showCars() {
    String leftAlignFormatHeader = "%-4s\t\t%-4s\t\t%-4s\t\t%-4s\t\t%n";
    String leftAlignFormatAttribute = "%-4s\t\t%-4s\t\t%-4s\t\t%-4s\t\t%n";
    String leftAlignFormatTruckAttribute = "%-4s\t\t%-4s\t\t%-4s\t\t%-4d\t\t%n";
    System.out.println("欢迎使用本汽车管理系统");
    System.out.printf(leftAlignFormatHeader, "车辆名称", "车辆颜色", "座位数", "载重量");
    for (Car car:cars) {
      if (car instanceof Truck) {
        Truck truck = (Truck) car;
        System.out.printf(leftAlignFormatTruckAttribute, car.getName(), car.getColor(), car.seatNum(), truck.getWeight());
      } else {
        System.out.printf(leftAlignFormatAttribute, car.getName(), car.getColor(), car.seatNum(), "不能拉货");
      }
    }
    System.out.println("1.小轿车\t2.大巴车\t3.卡车");
    System.out.print("请选择要修改的车辆：");
    int num = scanner.nextInt();
    switch (num) {
      case 1:
        update("sedan");
        break;
      case 2:
        update("bus");
        break;
      case 3:
        update("truck");
        break;
      default:
        System.out.println("车辆不存在！");
        break;
    }
  }

  public void update(String type) {
    String name = null;
    String color = null;
    if (type.equals("sedan")) {
      System.out.print("输入车辆名称：");
      name = scanner.next();
      System.out.print("输入车辆颜色：");
      color = scanner.next();
      Sedan sedan = new Sedan(name, color);
      cars[0] = sedan;
    }
    if (type.equals("bus")) {
      System.out.print("输入车辆名称：");
      name = scanner.next();
      System.out.print("输入车辆颜色：");
      color = scanner.next();
      Bus bus = new Bus(name, color);
      cars[1] = bus;
    }
    if (type.equals("truck")) {
      System.out.print("输入车辆名称：");
      name = scanner.next();
      System.out.print("输入车辆颜色：");
      color = scanner.next();
      System.out.print("输入卡车载重量：");
      int weight = scanner.nextInt();
      if (weight > 12) {
        CarException carException = new CarException("卡车的载重量不能超过12吨");
        try {
          throw carException;
        } catch (CarException e) {
          e.printStackTrace();
          return;
        }
      }
      Truck truck = new Truck(name, color, weight);
      cars[2] = truck;
    }
    showCars();
  }

  public static void main(String[] args) {
    Test test = new Test();
    test.showCars();
  }
}
```

讲解了面向对象的高级部分、包括了Object类、包装类、接口和异常。其中Object类是所有Java类的父类，定义了Java体系的基础资料，通过传承传递给Java的每一个类，通过方法重写和多态让整个Java体系具有很强的灵活性。

包装类是Java为基本数据类型提供封装的一组类，通过包装类我们可以将基本数据类型转为对象，这一点在面向对象编程中很重要。

接口是抽象类的扩展，是Java中实现多态的重要方式，可以降低程序的耦合性，让程序变得更加灵活多变，接口就相当于零件，我们可以自由地将这些零件进行组装、集合。

异常时Java中处理错误的一种机制，同样是基于面向对象的思想，将错误抽象成对象然后进行处理，这里需要关注的是对异常相关的几个关键字的使用，try、catch、finally、throw、throws。
