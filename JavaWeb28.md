### 数据库连接池

JDBC开发流程

- 加载驱动（只需要加载一次）
- 建立数据库连接（Connection）
- 执行SQL语句（Statement）
- ResultSet接收结果集（查询）
- 断开链接，释放资源

数据库连接对象是通过DriverManager来获取的，每次获取都需要向数据库申请获取连接，验证用户名和密码

执行完SQL语句后断开链接，这样的方式会造成资源的浪费，数据库连接资源没有得到很好的重复利用。

可以使用数据库连接池解决这一问题。

数据库连接池的基本思想就是为数据库建立一个缓冲池，预先向缓冲池中放入一定数量的连接对象，当需要获取数据库连接的时候，只需要从缓冲池中取出一个对象，用完之后再放回缓冲池中，供下一次请求使用，做到了资源的重复利用。允许程序重复使用一个现有的数据库连接对象，而不需要重新创建。

当数据库连接池中没有空闲的连接时，新的请求就会进入等待队列，等待其他线程释放连接。

#### 数据库连接池实现

JDBC的数据库连接池使用javax.sql.DataSource接口来完成的。DataSource是Java官方提供的接口，使用的时候开发者不需要自己来实现接口，可以使用第三方的工具，C3P0是一个常用的第三方实现，实际开发中直接使用C3P0即可完成数据库连接池的操作。

1. 导入jar包

传统方式拿到的Connection

```shell
com.mysql.cj.jdbc.ConnectionImpl@4ac3c60d
```

```java
package com.example;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConnectionTest {
  public static void main(String[] args) {
    try {
      Class.forName("com.mysql.cj.jdbc.Driver");
      String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&charactorEncoding=UTF-8";
      String user = "root";
      String password = "password";
      Connection connection = DriverManager.getConnection(url, user, password);
      System.out.println(connection);
      //释放资源
      connection.close();
    } catch (ClassNotFoundException | SQLException e) {
      e.printStackTrace();
    }
  }
}
```

C3P0拿到的Connetion

```shell
com.mchange.v2.c3p0.impl.NewProxyConnection@58fdd99 [wrapping: com.mysql.cj.jdbc.ConnectionImpl@6b1274d2]
```

```java
package com.example;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.beans.PropertyVetoException;
import java.sql.Connection;
import java.sql.SQLException;

public class DataSourceTest {
  public static void main(String[] args) {
    try {
      //创建c3p0
      ComboPooledDataSource dataSource = new ComboPooledDataSource();
      //加载驱动
      dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
      dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test?useUnicode=true&charactorEncoding=UTF-8");
      dataSource.setUser("root");
      dataSource.setPassword("password");
      //设置初始化连接个数
      dataSource.setInitialPoolSize(20);
      //设置连接池最小连接数，20-10时（该申请了）再向DB申请（最小空闲连接数）
      dataSource.setMinPoolSize(10);
      //设置最大连接数
      dataSource.setMaxPoolSize(30);
      //当连接对象不够时，再次申请的连接对象个数
      dataSource.setAcquireIncrement(5);
      Connection connection = dataSource.getConnection();
      System.out.println(connection);
      //还回到数据库连接池中
      connection.close(); //还回去
    } catch (PropertyVetoException | SQLException e) {
      e.printStackTrace();
    }
  }
}
```

C3P0优化了

- 加载驱动（只需要加载一次）
- 建立数据库连接（Connection）

实际开发中，将C3P0多配置信息定义在xml文件中，Java程序只需要加载配置文件即可完成数据库连接池的初始化操作

1. 配置文件的名字必须是c3p0.config.xml
2. 初始化ComboPooledDataSource时，传入的参数必须是c3p0-config.xml中named-config标签的name属性值

```java
package com.example;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.sql.Connection;
import java.sql.SQLException;

public class DataSourceTest {
  public static void main(String[] args) {
    try {
      //创建c3p0
      ComboPooledDataSource dataSource = new ComboPooledDataSource("testc3p0");
      //加载驱动
      Connection connection = dataSource.getConnection();
      System.out.println(connection);
      //还回到数据库连接池中
      connection.close(); //还回去
    } catch (SQLException e) {
      e.printStackTrace();
    }
  }
}
```

resource文件夹，文件名是写死的。传入`<named-config>`的`name`属性

resource/c3p0-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<c3p0-config>
    <named-config name="testc3p0">
        <!-- 指定连接数据源的基本属性 -->
        <property name="user">root</property>
        <property name="password">password</property>
        <property name="driverClass">com.mysql.cj.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/test?useUnicode=true&amp;charactorEncoding=UTF-8</property>

        <!-- 若数据库中连接数不足时，一次向数据库服务器申请多少个连接-->
        <property name="acquireIncrement">5</property>
        <!-- 初始化数据库连接池时连接的数量 -->
        <property name="initialPoolSize">5</property>
        <!-- 数据库连接池中的最小的数据库连接数 -->
        <property name="minPoolSize">5</property>
        <!-- 数据库连接池中的最大的数据库连接数 -->
        <property name="maxPoolSize">10</property>
    </named-config>
</c3p0-config>
```

### DBUtils

DBUtils可以帮助开发者完成数据的封装（结果集到Java对象的映射）

1. 导入jar包

ResultSetHandler接口是用来处理结果集，可以将查询到的结果集转变成Java对象，提供了4种实现类。

- BeanHandler将结果集映射成Java对象
- BeanListHandler将结果集映射成List集合List<Student>
- MapHandler将结果集映射成Map对象
- MapListHandler将结果集映射成MapList集合

```java
public static Student findByDBUtils(Integer id){
	Connection connection = null;
  Student student = null;
  try {
    connection = dataSource.getConnection();
    String sql = "select * from student";
    QueryRunner queryRunner = new QueryRunner();
    List<Map<String, Object>> list = queryRunner.query(connection, sql, new MapListHandler());
    for (Map<String, Ojbect> map : list){
      System.out.println(map);
    }
  } catch (SQLException e) {
    e.printStackTree();
  } finally {
    try {
      connection.close();
    } catch (SQLException e){
      e.printStackTree();
    }
  }
  return student;
}
```

