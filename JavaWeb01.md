# Tomcat

web应用服务器：Tomcat、Jboss、Weblogic、Jetty

- 安装Tomcat

  ```shell
  brew install tomcat
  ```

  bin：存放各个平台（操作系统）下启动和停止Tomcat服务的脚本文件。bat --> windows， sh --> linux

  conf：存放各种Tomcat服务器的配置文件

  lib：存放Tomcat服务器所需要的jar文件

  log：存放Tomcat服务运行的日志

  temp：Tomcat运行时的临时文件
  
  webapps：存放允许客户端访问的资源（java程序）。
  
  work：存放Tomcat将JSP转换之后的Servlet文件

![tomcatIDEA开始](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/tomcatIDEA开始.png)

![tomcatIDEA开始02](/Users/anasiangangster/Desktop/Java培训/楠哥/picture/tomcatIDEA开始02.png)

## Servlet

- 什么是Servlet？

  Servlet是Java Web开发的基石，与平台无关的服务器组件。它是运行在Servlet容器/Web应用服务器/Tomcat，负责与客户端进行通信。

- Servlet的功能：
  1. 创建并返回基于客户请求的动态HTML页面。
  2. 与数据库进行通信。

- 如何使用Servlet？

  Servlet本事是一组接口，javax.servlet，java.lang，java.util，javax.sql（java是基本的东西，javax是扩展的东西）

  自定义一个类，并且实现Servlet接口，这个类就具备了接受客户端请求以及做出相应的功能。

WEB-INF/web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

  <servlet>
    <servlet-name>MyServlet</servlet-name>
    <servlet-class>com.anasiangangster.servlet.MyServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>MyServlet</servlet-name>
    <url-pattern>/myservlet</url-pattern>
  </servlet-mapping>
</web-app>
```

MyServlet.java

```java
package com.anasiangangster.servlet;

import javax.servlet.*;
import java.io.IOException;

public class MyServlet implements Servlet {
  @java.lang.Override
    public void init(ServletConfig servletConfig) throws ServletException {

  }

  @java.lang.Override
    public ServletConfig getServletConfig() {
    return null;
  }

  @java.lang.Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
    String id = servletRequest.getParameter("id"); // String 可以接收null
    System.out.println("我是Servlet，我已经接收到了客户端发来的请求，参数是"+id);
    servletResponse.setContentType("text/html; charset=UTF-8");
    servletResponse.getWriter().write("你好客户端，我已经接收到你的请求");
  }

  @java.lang.Override
    public java.lang.String getServletInfo() {
    return null;
  }

  @java.lang.Override
    public void destroy() {

  }
}
```

浏览器不能直接访问Servlet文件，只能通过映射到方式来间接访问Servlet，映射需要开发者手动配置，有两种配置方式。

- 基于XML文件的配置方式

- 基于注解的方式

  ```java
  @WebServlet("/myservlet")
  public class MyServlet implements Servlet {
    
  }
  ```

上述两种配置方式结果完全一致，将myservlet与MyServlet进行映射，即在浏览器地址栏中直接访问/myservlet就可以映射到MyServlet

### Servlet的生命周期

1. 当浏览器访问Servlet的时候，Tomcat会查询当前Servlet的实例化对象是否存在，如果不存在，则通过反射机制动态创建对象，如果存在，直接执行第3步
2. 调用init方法完成初始化操作
3. 调用service方法完成业务逻辑操作
4. 关闭Tomcat时，会调用destory方法，释放当前对象所占用的资源

#### Servlet的生命周期方法

无参构造函数、init、service、destory

- 无参构造函数只调用一次，初始化对象
- init只调用一次，初始化对象
- service调用n次，执行业务方法
- destroy只调用一次，卸载对象

#### ServletConfig

该接口时用来描述Servlet的基本信息的。

getServletName() 返回Servlet的名称，全类名（带着包名的类名）

getInitParameters(String key) 获取init参数的值（web.xml）

getInitParameterNames() 返回所用的initParameter的name值，一般用作遍历初始化参数

getServletContext() 返回ServletContext对象，它是Servlet的上下文，整个Servlet的管理者，整个Servlet的服务信息。

ServletConfig和ServletContext的区别：

ServletConfig作用于某个Servlet实例，每个Servlet都有对应的ServletConfig；ServletContext作用于整个Web应用，一个Web应用对应一个ServletContext，多个Servlet实例对应一个ServletContext。

### Servlet的层次结构

Servlet --> GenericServlet --> HttpServlet

HTTP请求有很多中类型，常用的有四种：

GET 读取

POST 保存

PUT 修改

DELETE 删除

GenericServlet实现Servlet接口，同时为它的子类屏蔽了不常用的方法，子类只需要重写service方法即可。

HttpServlet继承GenericServlet，根据请求类型进行分发处理，GET进入doGet方法，POST进入doPost方法。

开发者自定义的Servlet类只需要继承HttpServlet即可，重写doGet和doPost。

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/test")
public class TestServlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.getWriter().write("GET");
  }

  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.getWriter().write("POST");
  }
}
```

## JSP

JSP本质上就是一个Servlet，JSP主要负责与用户交互，将最终的界面呈现给用户，HTML+JS+CSS+Java的混合文件。

当服务器接收到一个后缀是jsp的请求时，将该请求交给JSP引擎去处理，每一个JSP页面第一次被访问的时候，JSP引擎会将它翻译成一个Servlet文件，再由Web容器调用Servlet完成响应。

单纯从开发的角度看，JSP就是在HTML中嵌入Java程序。

具体的嵌入方式有3种：

1. JSP脚本

   执行Java逻辑代码

   ```jsp
   <% //Java代码 %>
   ```

2. JSP声明

   定义Java方法

   ```jsp
   <%!
     //声明Java方法
   %>
   ```

3. JSP表达式

   把Java对象直接输出到HTML页面中

   ```jsp
   <%=
     //Java变量
   %>
   ```

```jsp
<%!
  public String test() {
  return "Hello World";
}
%>
<%
	String str = test();
%>
<%=str%>
```

```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page import="com.anasiangangster.entity.User" %>
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
    <h1>Index</h1>
    <%
    // List<String> names = new ArrayList<>();
    // names.add("张三");
    // names.add("李四");
    // names.add("王五");
    // List<Integer> ages = new ArrayList<>();
    // ages.add(21);
    // ages.add(21);
    // ages.add(22);
    List<User> list = new ArrayList<>();
    list.add(new User("张三", 21));
    list.add(new User("李四", 23));
    list.add(new User("王五", 22));
    %>
    <table>
      <tr>
        <th>姓名</th>
        <th>年龄</th>
      </tr>

      <%
      for(int i = 0; i < list.size(); i++) {
      %>
        <tr>
          <td><%=list.get(i).getName()%></td>
          <td><%=list.get(i).getAge()%></td>
        </tr>
      <%
      }
      %>
    </table>
  </body>
</html>
```

### JSP内置对象

1. request：表示一次请求，HttpServletRequest

2. response：表示一次响应，HttpServletResponse

3. pageContext：页面上下文，获取页面信息，PageContext。

4. session：表示一次会话，浏览器开始会话开始，互交几次，关闭浏览器会话结束，保存用户信息，HttpSession。

5. application：表示当前Web应用，全局对象，保存所有用户共享信息，ServletContext。

6. config：当前JSP对应的Servlet的ServletConfig对象，获取当前Servlet的信息。

7. out：向浏览器输出数据，JspWriter

8. page：当前JSP对应的Servlet对象，Servlet

9. exception：表示JSP页面发生的异常，Exception

常用的是request、response、session、application、pageContext

#### request常用方法

1. String getParameter(String key) 获取客户端传来的参数

2. void setAttribute(String key, Object value) 通过键值对的形式保存数据

3. Object getAttrbute(String key) 通过key取出value 

4. RequestDispatcher getRequestDispatcher(String path) 返回一个RequestDispatcher对象，该对象的forward方法用于请求转发。

5. String[] getParameterValues() 获取客户端传来的多个同名参数

   ```jsp
   <%
   	// localhost:8080/index.jsp?name=aobo&name=karen&name=jake&name=傻逼
   	String[] names = request.getParameterValues("name");
   %>
   <%=Arrays.toString(names)%>
   ```

6. void setCharacterEncoding(String charset) 指定每个请求的编码

#### response常用方法

1. sendRedirect(String path) 重定向，页面之间的跳转。

转发getRequestDispatcher和重定向sendRedirect的区别：

转发是将同一个请求传给下一个页面；重定向是创建一个新的请求传给下一个页面，之前的请求结束生命周期。

转发：同一个请求在服务器之间传递，地址栏不变，这叫服务器跳转。

重定向：由客户端发送一次新的请求来访问跳转后的资源目标，地址栏改变，也叫客户端跳转

如果两个页面之间需要通过request来传值，则必须使用转发，不能使用重新定向

用户登录，如果用户名和密码正确，则跳转到首页（转发），并且展示用户名，否则重新回到登录页面（重定向）

### HTTP请求状态码

200：正常

404：资源找不到

400：请求类型不匹配

500：Java程序抛出异常（Java代码有问题）

