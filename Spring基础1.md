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

- singleton：单例，表示通过Spring容器（IoC）获取的bean是唯一的。（默认是单例）
- protoype：原型，表示通过Spring容器获取的bean是不同的。
- request：请求，表示在一次HTTP请求内有效
- session：会话，表示在一个用户会话内有效

request和session只适用于Web项目，大多数情况下，使用单例和原型较多。

