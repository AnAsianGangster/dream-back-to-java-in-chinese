### Spring框架两大核心机制

- **IoC**（控制反转）/ DI（依赖注入）
- **AOP**（面向切面编程）

Spring是一个企业级开发框架，是软件设计层面的框架，优势在于可以将应用程序进行分层，开发者可以自己自主选择组件。

MVC：Struts2、Spring MVC

ORMMapping：Hibernate、MyBatis、Spring Data

为什么使用Spring

企业级项目特点

- 大规模：用户数量多、数据规模大、功能模块众多
- 性能和安全要求高
- 业务复杂
- 灵活多变

Spring概述

- Spring是一个企业级开发框架，关注点在于软件设计层面
- Spring已经成为Java领域的行业标准
- Spring提供了各个层面的解决方案，Spring MVC、Spring Data、Spring Cloud
- Spring两大核心机制IoC（控制反转）、AOP（面向切面）

![spring-overview](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/spring-overview.png)

Spring优点

- 低侵入式设计
- 独立于各种应用服务器
- 依赖注入特性将组件关系透明化，降低了耦合度
- 面向切面编程特性允许将任务进行集中式处理
- 与第三方框架的良好整合

Spring核心技术

- 控制反转（IoC：Inversion of Control）/ 依赖注入（DI：Dependency Injection）
- 面向切面编程（AOP：Aspect Oriented Programming）

### Spring IoC

什么是控制反转

在传统的程序开发中，需要调用对象时，通常由调用者来创建被调用者的实例，即对象是由调用者主动new出来的。

但在Spring框架中创建对象的工作不再由调用者来完成，而是交给IoC容器来创建，再推送给调用者，整个流程完成反转，所以是控制反转 

![SpringBeginner1](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/SpringBeginner1.svg)

### 如何使用IoC

- 创建Maven工程，pom.xml添加依赖

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>org.example</groupId>
      <artifactId>springbeginner1</artifactId>
      <version>1.0-SNAPSHOT</version>
  
      <properties>
          <maven.compiler.source>13</maven.compiler.source>
          <maven.compiler.target>13</maven.compiler.target>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context</artifactId>
              <version>5.3.8</version>
          </dependency>
      </dependencies>
  </project>
  ```

- 创建实体类

  ```java
  package com.anasiangangster.entity;
  
  import lombok.Data;
  
  @Data
  public class Student {
    private long id;
    private String name;
    private int age;
  }
  ```

- 传统的开发方式，手动new Student

  ```java
  Student student = new Student(1L, "杨奥博", 25);
  ```

- 通过IoC创建对象，再配置文件中添加需要管理的对象，XML格式的配置文件，文件名可以自定义。

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
             http://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="student" class="com.anasiangangster.entity.Student">
          <property name="id" value="2"></property>
          <property name="name" value="杨奥博2"></property>
          <property name="age" value="25"></property>
      </bean>
  
  </beans>
  ```

- 从IoC中获取对象，通过id获取

  ```java
  //加载配置文件
  ApplicationContext applicationContext = new ClassPathXmlApplicationContext("entities.xml");
  Student studentBean = (Student) applicationContext.getBean("student");
  System.out.println(studentBean);
  ```

### 配置文件

- 通过配置bean标签来完成对象的管理

  - `id`：对象名
  - `class`：对象的模版类。（所有交给IoC容器来管理的类必须有无参构造函数，因为Spring底层时通过反射机制来创建对象，调用的是无参构造）

- 对象的成员变量通过`property`标签完成赋值。

  - `name`：成员变量名

  - `value`：成员变量值（基本数据类型、String可以直接赋值，如果是其他的引用类型，不能通过value赋值)

  - `ref`：将IoC中的另外一个bean赋给当前的成员变量（DI）

    ```xml
    <bean id="student" class="com.anasiangangster.entity.Student">
      <property name="id" value="2"></property>
      <property name="name" value="杨奥博2"></property>
      <property name="age" value="25"></property>
      <property name="address" ref="address"></property>
    </bean>
    
    <bean id="address" class="com.anasiangangster.entity.Address">
      <property name="id" value="1"></property>
      <property name="name" value="樟宜上段"></property>
    </bean>
    ```

### IoC底层原理

- 读取配置文件，解析XML
- 通过反射机制来实例化配置文件中所配置的所有的bean

```java
package com.anasiangangster.ioc;

public interface ApplicationContext {
  public Object getBean(String id);
}
```

```java
package com.anasiangangster.ioc;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;
import org.xml.sax.SAXException;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Locale;
import java.util.Map;

public class ClassPathXmlApplicationContext implements ApplicationContext{
  private static Map<String, Object> ioc = null;

  static {
    ioc = new HashMap<>();
  }

  public ClassPathXmlApplicationContext(String path){
    try {
      SAXReader reader = new SAXReader();
      Document document = reader.read("./src/main/resources/"+path);
      Element root = document.getRootElement();
      Iterator<Element> iterator = root.elementIterator();
      while(iterator.hasNext()){
        Element element = iterator.next();
        String id = element.attributeValue("id");
        String className = element.attributeValue("class");
        //通过反射机制创建对象
        Class clazz = Class.forName(className);
        //获取无参构造函，创建目标对象
        Constructor constructor = clazz.getConstructor();
        Object object = constructor.newInstance();
        //给目标对象赋值
        Iterator<Element> beanIterator = element.elementIterator();
        while(beanIterator.hasNext()) {
          Element property = beanIterator.next();
          String name = property.attributeValue("name");
          String valueStr = property.attributeValue("value");
          String ref = property.attributeValue("ref");
          if (ref == null) {
            String methodName = "set"+name.substring(0, 1).toUpperCase()+name.substring(1);
            Field field = clazz.getDeclaredField(name);
            Method method = clazz.getDeclaredMethod(methodName, field.getType());
            //根据成员变量的数据类型将value进行转换
            Object value = null;
            switch(field.getType().getName()) {
              case "long":
                value = Long.parseLong(valueStr);
                break;
              case "java.lang.String":
                value = valueStr;
                break;
              case "int":
                value = Integer.parseInt(valueStr);
                break;
            }
            method.invoke(object, value);
          } else {
            //依赖注入
          }
          ioc.put(id, object);
        }
      }
      System.out.println(ioc);
    } catch (DocumentException e) {
      e.printStackTrace();
    } catch (ClassNotFoundException e) {
      e.printStackTrace();
    } catch (NoSuchMethodException e) {
      e.printStackTrace();
    } catch (InvocationTargetException e) {
      e.printStackTrace();
    } catch (InstantiationException e) {
      e.printStackTrace();
    } catch (IllegalAccessException e) {
      e.printStackTrace();
    } catch (NoSuchFieldException e) {
      e.printStackTrace();
    }
  }

  @Override
  public Object getBean(String id) {
    return ioc.get(id);
  }
}
```

```java
package com.anasiangangster.ioc;

import com.anasiangangster.entity.Student;

public class Test {
  public static void main(String[] args) {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("entities.xml");
    Student student = (Student) applicationContext.getBean("student");
    //        System.out.println(student);
  }
}
```

### 通过运行时类获取bean

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("entities.xml");
Student studentBean = (Student) applicationContext.getBean(Student.class);
System.out.println(studentBean);
```

通过这种方式存在一个问题，配置文件夹中一个数据类型的对象只能有一个实例，否则会抛出异常，因为没有唯一的bean。

### 通过有参构造创建bean

- 在实体类中创建对应的有参构造

- 配置文件，按顺序写就可以省略`name`属性，用`index`下标也可以

  ```xml
  <bean id="student3" class="com.anasiangangster.entity.Student">
    <constructor-arg name="id" value="4"></constructor-arg>
    <constructor-arg value="李四"></constructor-arg>
    <constructor-arg index="3" value="32"></constructor-arg>
    <constructor-arg name="address" ref="address"></constructor-arg>
  </bean>
  ```

### 给bean注入集合

```xml
<bean id="student3" class="com.anasiangangster.entity.Student">
  <constructor-arg name="id" value="4"></constructor-arg>
  <constructor-arg name="name" value="李四"></constructor-arg>
  <constructor-arg name="age" value="32"></constructor-arg>
  <constructor-arg name="addresses">
    <list>
      <ref bean="address"></ref>
      <ref bean="address2"></ref>
    </list>
  </constructor-arg>
</bean>

<bean id="student" class="com.anasiangangster.entity.Student">
  <property name="id" value="2"></property>
  <property name="name" value="杨奥博2"></property>
  <property name="age" value="25"></property>
  <property name="addresses">
    <list>
      <ref bean="address"></ref>
      <ref bean="address2"></ref>
    </list>
  </property>
</bean>

<bean id="address2" class="com.anasiangangster.entity.Address">
  <property name="id" value="2"></property>
  <property name="name" value="实龙岗"></property>
</bean>

<bean id="address" class="com.anasiangangster.entity.Address">
  <property name="id" value="1"></property>
  <property name="name" value="樟宜上段"></property>
</bean>
```

### scope 作用域

Spring管理的bean是根据scope来生成的，表示bean的作用域，共4种。

- singleton：单例，表示通过Spring容器（IoC）获取的bean是唯一的。（默认是单例）（单例默认饿汉，原型默认懒汉）
- protoype：原型，表示通过Spring容器获取的bean是不同的。
- request：请求，表示在一次HTTP请求内有效
- session：会话，表示在一个用户会话内有效

request和session只适用于Web项目，大多数情况下，使用单例和原型较多。

prototype模式当业务代码获取IoC容器中的bean时，Spring才去调用无参构造创建对应的bean

singleton模式无论业务代码是否获取IoC容器中的bean，Spring在加载spring.xml时就会创建bean

### Spring的继承

与Java的继承不同，Java是类层面的继承，子类可以继承父类的内部结构信息；Spring是对象层面的继承，子对象可以继承父对象的属性值

```xml
<bean id="address2" class="com.anasiangangster.entity.Address">
  <property name="id" value="2"></property>
  <property name="name" value="实龙岗"></property>
</bean>

<bean id="address" class="com.anasiangangster.entity.Address">
  <property name="id" value="1"></property>
  <property name="name" value="樟宜上段"></property>
</bean>

<bean id="student2" class="com.anasiangangster.entity.Student" scope="singleton">
  <property name="id" value="1"></property>
  <property name="name" value="张三"></property>
  <property name="age" value="34"></property>
  <property name="addresses">
    <list>
      <ref bean="address"></ref>
      <ref bean="address2"></ref>
    </list>
  </property>
</bean>

<bean id="student2.1" class="com.anasiangangster.entity.Student" parent="student2">
  <property name="name" value="稳稳"></property>
</bean>
```

Spring继承的关注点在于具体点对象，而不在于类，即不同的两个类的实例化对象可以完成继承，前提是子对象必须包含父对象的所有属性，同时可以在此基础上添加其他属性。

### Spring的依赖

创建顺序，默认在上面的bean优先。

与继承类似，依赖也是描述bean和bean之间的一种关系，配置依赖之后，被依赖的bean一定先创建，再创建依赖的bean，A依赖于B，先创建B，再创建A

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
  <bean id="user" class="com.anasiangangster.entity.User" depends-on="student"></bean>
  <bean id="student" class="com.anasiangangster.entity.Student"></bean>
</beans>
```

###  Spring的p命名空间

p命名空间是对IoC/DI的简化，使用p命名空间可以更加方便的完成bean的配置以及bean之间的依赖注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context">

  <bean id="student" class="com.anasiangangster.entity.Student" p:id="23" p:name="阿斯顿个" p:age="243" p:address-ref="address"></bean>
  <bean id="address" class="com.anasiangangster.entity.Address" p:id="23" p:name="喇叭路"></bean>
</beans>
```

### Spring的工厂方法

IoC通过工厂模式创建bean的方式有两种：

- 静态工厂方法
- 实例工厂方法

静态工厂方法

```java
package com.anasiangangster.entity;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Car {
  private long id;
  private String name;
}
```

```java
package com.anasiangangster.factory;

import com.anasiangangster.entity.Car;

import java.util.HashMap;
import java.util.Map;

public class StaticCarFactory {
  private static Map<Long, Car> carMap;

  static {
    carMap = new HashMap<>();
    carMap.put(1L, new Car(1L, "宝马"));
    carMap.put(2L, new Car(2L, "奔驰"));
  }

  public static Car getCar(long id) {
    return carMap.get(id);
  }
}
```

```xml
<!-- 配置静态工厂创建Car -->
<bean id="car" class="com.anasiangangster.factory.StaticCarFactory" factory-method="getCar">
  <constructor-arg value="2"></constructor-arg>
</bean>
```

实例工厂方法

```java
package com.anasiangangster.factory;

import com.anasiangangster.entity.Car;

import java.util.HashMap;
import java.util.Map;

public class InstanceCarFactory {
  private Map<Long, Car> carMap;

  public InstanceCarFactory() {
    this.carMap = new HashMap<>();
    this.carMap.put(1L, new Car(1L, "宝马"));
    this.carMap.put(2L, new Car(2L, "奔驰"));
  }

  public Car getCar(long id) {
    return this.carMap.get(id);
  }
}
```

```xml
<!-- 配置实例工厂bean -->
<bean id="InstanceCarFactory" class="com.anasiangangster.factory.InstanceCarFactory"></bean>
<!-- 配置实例工厂创建的car -->
<bean id="car2" factory-bean="InstanceCarFactory" factory-method="getCar">
  <constructor-arg value="1"></constructor-arg>
</bean>
```

### IoC自动装载（Autowire)

IoC负责创建对象，DI负责完成对象的依赖注入，通过配置property标签的ref属性来完成，同时Spring提供了另外一种更加简便的依赖注入方式：自动装载，不需要手动配置property，IoC容器会自动选择bean完成注入。

自动装载有两种方式：

- byName：通过属性名自动装载
- byType：通过属性的数据类型自动装载

byName

```xml
<bean id="car" class="com.anasiangangster.entity.Car">
  <property name="id" value="1"></property>
  <property name="name" value="马自达"></property>
</bean>

<bean id="person" class="com.anasiangangster.entity.Person" autowire="byName">
  <property name="id" value="11"></property>
  <property name="name" value="杨奥博是王"></property>
</bean>
```

byType，只能有唯一的对象，不然（`org.springframework.beans.factory.UnsatisfiedDependencyException`）

```xml
<bean id="cars" class="com.anasiangangster.entity.Car">
  <property name="id" value="1"></property>
  <property name="name" value="马自达"></property>
</bean>

<bean id="person" class="com.anasiangangster.entity.Person" autowire="byType">
  <property name="id" value="11"></property>
  <property name="name" value="杨奥博是王"></property>
</bean>
```

byType需要注意，如果同时存在两个及以上的符合条件的bean时，自动装载会抛出异常

### AOP

AOP：Aspect Oriented Programming面向切面编程

AOP是面向对象编程的一个补充，在运行时，动态地将代码切入到类的指定方法、指定位置上的思想就是面向切面编程。将不同方法的同一个位置抽象成一个切面对象，对该切面对象进行编程就是AOP。

AOP优点

- 降低模块之间的耦合度
- 使系统容易扩展
- 更好的代码复用
- 非业务代码更加集中，不分散，便于统一管理
- 业务代码更简洁存粹，没有其他代码的影响

![aop](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/aop.gif)

- 将复杂的需求分解成不同的方面，将散布在系统中的公共功能集中解决

### 如何使用

- 创建Maven工程，pom.xml添加

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aop</artifactId>
  <version>5.3.8</version>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>5.2.8.RELEASE</version>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.3.8</version>
</dependency>
```

- 创建一个计算器接口Cal，定义4个方法

```java
package com.anasiangangster.utils;

public interface Cal {
  public int add(int num1, int num2);
  public int sub(int num1, int num2);
  public int mul(int num1, int num2);
  public int div(int num1, int num2);
}
```

- 创建接口的实现类CalImpl

```java
package com.anasiangangster.utils.impl;

import com.anasiangangster.utils.Cal;

public class CalImpl implements Cal {
  @Override
  public int add(int num1, int num2) {
    System.out.println("add方法的参数是["+num1+", "+num2+"]");
    int result = num1 + num2;
    System.out.println("add方法的结果的"+result);
    return result;
  }

  @Override
  public int sub(int num1, int num2) {
    System.out.println("sub方法的参数是["+num1+", "+num2+"]");
    int result = num1 - num2;
    System.out.println("sub方法的结果的"+result);
    return result;
  }

  @Override
  public int mul(int num1, int num2) {
    System.out.println("mul方法的参数是["+num1+", "+num2+"]");
    int result = num1 * num2;
    System.out.println("mul方法的结果的"+result);
    return result;
  }

  @Override
  public int div(int num1, int num2) {
    System.out.println("div方法的参数是["+num1+", "+num2+"]");
    int result = num1 / num2;
    System.out.println("div方法的结果的"+result);
    return result;
  }
}
```

上述代码中，日志信息的业务逻辑的耦合性很高，不利于系统的维护，使用AOP可以进行优化，如何实现AOP？使用动态代理的方式来实现。

给业务代码找一个代理，打印日志信息的工作交给代理来做，这样的话业务代码只需要关注自身的业务即可。

```java
package com.anasiangangster.utils;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class MyInvocationHandler implements InvocationHandler {
  //接收委托对象
  private Object object = null;

  //返回代理对象
  public Object bind(Object object) {
    this.object = object;
    return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), this);
  }

  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    System.out.println(method.getName()+"方法的参数是"+ Arrays.toString(args));
    Object result = method.invoke(this.object, args);
    System.out.println(method.getName()+"的结果是"+result);
    return result;
  }
}
```

以上是通过动态代理实现AOP的过程，比较复杂，不好理解，Spring框架对AOP进行了封装，使用Spring框架可以使用面向对象的思想来实现AOP

Spring框架中不需要创建InvocationHandler，只需要创建一个切面对象，将所有的非业务代码在切面对象中完成即可。Spring框架底层会自动根据切面类以及目标类生成一个代理对象。

LoggerAspect

```java
package com.anasiangangster.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
@Aspect
public class LoggerAspect {
  @Before("execution(public int com.anasiangangster.utils.impl.CalImpl.*(..))")
  public void before(JoinPoint joinPoint) {
    //获取方法名
    String name = joinPoint.getSignature().getName();
    //获取参数
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println(name+"方法的参数是："+args);
  }
}
```

LoggerAspect类定义处添加的两个注解：

- `@Aspect`：表示该类是切面类。
- `@Component`：将该类的对象注入到IoC容器。

具体方法处添加的注解：

- `@Before`：表示方法执行的具体位置和时机

CalImpl也需要添加`@Component`，交给IoC容器来管理

```java
package com.anasiangangster.utils.impl;

import com.anasiangangster.utils.Cal;
import org.springframework.stereotype.Component;

@Component
public class CalImpl implements Cal {
  @Override
  public int add(int num1, int num2) {
    int result = num1 + num2;
    return result;
  }

  @Override
  public int sub(int num1, int num2) {
    int result = num1 - num2;
    return result;
  }

  @Override
  public int mul(int num1, int num2) {
    int result = num1 * num2;
    return result;
  }

  @Override
  public int div(int num1, int num2) {
    int result = num1 / num2;
    return result;
  }
}
```

spring.xml中配置AOP

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

  <!-- 自动扫描 -->
  <context:component-scan base-package="com.anasiangangster"></context:component-scan>
  <!-- 使Aspect注解生效，为目标类自动生成代理对象 -->
  <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

`context:component-scan`将`com.anasiangangster`包中的所有类进行扫描，如果该类同时添加了`@Component`，则将该类扫描到IoC容器中，即IoC管理它的对象。

`aop:aspectj-autoproxy`让Spring框架结合切面类和目标类自动生成动态代理对象。

```java
package com.anasiangangster.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
@Aspect
public class LoggerAspect {
  @Before(value = "execution(public int com.anasiangangster.utils.impl.CalImpl.*(..))")
  public void before(JoinPoint joinPoint) {
    //获取方法名
    String name = joinPoint.getSignature().getName();
    //获取参数
    String args = Arrays.toString(joinPoint.getArgs());
    System.out.println(name+"方法的参数是："+args);
  }

  @After(value = "execution(public int com.anasiangangster.utils.impl.CalImpl.*(..))")
  public void after(JoinPoint joinPoint) {
    //获取方法名
    String name = joinPoint.getSignature().getName();
    System.out.println(name+"方法执行完成");
  }

  @AfterReturning(value = "execution(public int com.anasiangangster.utils.impl.CalImpl.*(..))", returning = "resulthere")
  public void afterReturning(JoinPoint joinPoint, Object resulthere){
    //获取方法名
    String name = joinPoint.getSignature().getName();
    System.out.println("这个方法的结果是"+resulthere);
  }

  @AfterThrowing(value = "execution(public int com.anasiangangster.utils.impl.CalImpl.*(..))", throwing = "exception")
  public void afterThrowing(JoinPoint joinPoint, Exception exception){
    //获取方法名
    String name = joinPoint.getSignature().getName();
    System.out.println(name+"方法抛出异常"+exception);
  }
}
```

- 切面：横切关注点被模块化的抽象对象
- 通知：切面对象完成的工作
- 目标：被通知的对象，即被横切的对象
- 代理：切面、通知、目标混合之后的对象
- 连接点：（JointPoint）通知要插入业务代码的具体位置
- 切点：AOP通过切点定位到连接点
