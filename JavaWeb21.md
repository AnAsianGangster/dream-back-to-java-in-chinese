# Ajax

Asynchronous JavaScript And XML 异步的javaScript和XML

AJAX不是新的编程，指的是一种交互方式，异步加载，客户端和服务器的数据交互更新在局部页面的技术，不需要刷新整个页面（局部刷新）

优点：

1. 局部刷新，效率更高
2. 用户体验更好

基于jQuery的AJAX

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>test</title>
    <script type="text/javascript" src="js/jQuery-3.6.0.min.js"></script>
    <script type="text/javascript">
        $(function () {
            var btn = $("#btn");
            btn.click(function(){
                $.ajax({
                    url:"/test",
                    type:"post",
                    data:"id=1",
                    dataType:"text",
                    success:function (data){
                        var text = $("#text")
                        text.before("<h1>"+data+"</h1>")
                    }
                })
            });
        })
    </script>
</head>
<body>
<%--    ${str}--%>
<%--    <form action="/test" method="post">--%>
<%--        <input type="text">--%>
<%--        <input type="submit" value="提交">--%>
<%--    </form>--%>
    <input id="text" type="text">
    <input id="btn" type="button" value="提交">
</body>
</html>
```

不能用表单提交请求，该用jQuery方式动态绑定事件来提交。<form>是同步的，AJAX是异步的

Servlet不能跳转到JSP，只能将数据返回

```java
package com.anasiangangster.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/test")
public class TestServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String id = req.getParameter("id");
        System.out.println(id);
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        String str = "Hello World";
//        req.setAttribute("str", str);
//        req.getRequestDispatcher("test.jsp").forward(req, resp);
        resp.getWriter().write(str);
    }
}
```

## 传统的Web数据交互 VS AJAX数据交互

- 客户端请求的方式不同：

传统，浏览器发送同步请求（form，a）

AJAX，异步引擎对象发送异步请求

- 服务器响应的方式不同：

传统，响应一个完整的JSP页面（视图）

AJAX，响应需要的数据（数据）

- 客户端处理方式不同：

传统，需要等待服务器完成响应，并且重新加载整个页面之后，用户才能进行后续操作，按照顺序走

AJAX，动态更新页面中的局部内容，不影响用户的其他操作

## AJAX原理

![javaweb01](/Users/anasiangangster/Desktop/Java培训/楠哥./picture//javaweb01.svg)

## 基于jQuery的AJAX语法

$.ajax((属性))

常用的属性：

url：请求的后端服务的地址

type：请求方式，默认get

data：请求参数，传给后台

dataType：服务器返回的数据类型，text/json

success：请求成功的回调函数

error：请求失败的回调函数，先error后complete

complete：请求完成的回调函数（无论成功或者失败，都会调用），先进complete再进success

## JSON

JavaScript Object Notation，一种轻量级的数据交互格式，完成js与java等后端开发语言对象数据之间的转换

客户端和服务器之间传递对象数据，需要用JSON格式

```java
package com.anasiangangster.entity;

public class User {
  private Integer id;
  private String name;
  private double score;

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

  public double getScore() {
    return score;
  }

  public void setScore(double score) {
    this.score = score;
  }

  public User(Integer id, String name, double score) {
    this.id = id;
    this.name = name;
    this.score = score;
  }

  @Override
  public String toString() {
    return "User{" +
      "id=" + id +
      ", name='" + name + '\'' +
      ", score=" + score +
      '}';
  }
}

//创建对象
User user = new User(1, "张三", 96.6);
```

```javas
var user = {
	id:1,
	name:"张三“,
	score:96.6
}
```

## AJAX实际应用

$()工厂函数

$(dom对象) --> jQuery对象

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>location</title>
    <script type="text/javascript" src="js/jQuery-3.6.0.min.js"></script>
    <script type="text/javascript">
        $(function(){
            //修改省
            $("#province").change(function() {
                var id = $(this).val();
                $.ajax({
                    url:"/location",
                    type:"post",
                    data:"id="+id+"&type=province",
                    dataType:"JSON",
                    success:function(data){
                        var content = "";
                        for (let i = 0; i < data.length; i++)
                            content += "<option>"+data[i]+"</option>";
                        $("#city").html(content).change();
                    }
                })
            })
            //修改城市
            $("#city").change(function(){
                var id = $(this).val();
                $.ajax({
                    url:"/location",
                    type:"post",
                    data:"id="+id+"&type=city",
                    dataType:"JSON",
                    success:function(data){
                        var content = "";
                        for (let i = 0; i < data.length; i++)
                            content += "<option value=\""+data[i]+"\">"+data[i]+"</option>";
                        $("#district").html(content);
                    }
                })
            })
        })
    </script>
</head>
<body>
    省：<select id="province">
        <option value="辽宁省">辽宁省</option>
        <option value="吉林省">吉林省</option>
        <option value="黑龙江省">黑龙江省</option>
    </select>
    市：<select id="city">
        <option value="沈阳市">沈阳市</option>
        <option value="大连市">大连市</option>
        <option value="盘锦市">盘锦市</option>
    </select>
    区：<select id="district">
        <option>铁西区</option>
        <option>和平区</option>
        <option>皇姑区</option>
    </select>
</body>
</html>
```

```java
package com.anasiangangster.servlet;

import net.sf.json.JSONArray;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@WebServlet("/location")
public class LocationServlet extends HttpServlet {
  private static Map<String, List<String>> cityMap;
  private static Map<String, List<String>> provinceMap;
  static {
    provinceMap = new HashMap<>();
    List<String> cities = new ArrayList<>();
    cities.add("沈阳市");
    cities.add("大连市");
    cities.add("盘锦市");
    provinceMap.put("辽宁省", cities);
    cities = new ArrayList<>();
    cities.add("长春市");
    cities.add("吉林市");
    cities.add("通化市");
    provinceMap.put("吉林省", cities);
    cities = new ArrayList<>();
    cities.add("哈尔滨市");
    cities.add("齐齐哈尔市");
    cities.add("绥化市");
    provinceMap.put("黑龙江省", cities);

    cityMap = new HashMap<>();
    //沈阳
    List<String> areas = new ArrayList<>();
    areas.add("铁西区");
    areas.add("和平区");
    areas.add("皇姑区");
    cityMap.put("沈阳市", areas);
    //大连
    areas = new ArrayList<>();
    areas.add("沙河口区");
    areas.add("西岗区");
    areas.add("中山区");
    cityMap.put("大连市", areas);
    //盘锦
    areas = new ArrayList<>();
    areas.add("双台子区");
    areas.add("兴隆台区");
    areas.add("大洼区");
    cityMap.put("盘锦市", areas);
    //长春
    areas = new ArrayList<>();
    areas.add("南关区");
    areas.add("宽城区");
    areas.add("朝阳区");
    cityMap.put("长春市", areas);
    //吉林市
    areas = new ArrayList<>();
    areas.add("船营区");
    areas.add("昌邑区");
    areas.add("龙潭区");
    cityMap.put("吉林市", areas);
    //通化市
    areas = new ArrayList<>();
    areas.add("东昌区");
    areas.add("二道江区");
    areas.add("梅河口市");
    cityMap.put("通化市", areas);
    //哈尔滨
    areas = new ArrayList<>();
    areas.add("道里区");
    areas.add("南岗区");
    areas.add("道外区");
    cityMap.put("哈尔滨市", areas);
    //齐齐哈尔
    areas = new ArrayList<>();
    areas.add("龙沙区");
    areas.add("建华区");
    areas.add("铁锋区");
    cityMap.put("齐齐哈尔市", areas);
    //绥化市
    areas = new ArrayList<>();
    areas.add("北林区");
    areas.add("安达市");
    areas.add("肇东市");
    cityMap.put("绥化市", areas);
  }

  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setCharacterEncoding("UTF-8");
    String type = req.getParameter("type");
    String id = req.getParameter("id");
    List<String> result = null;
    switch (type) {
      case "province":
        result = provinceMap.get(id);
        break;
      case "city":
        result = cityMap.get(id);
        String city = result.get(0);
        break;
    }
    JSONArray jsonArray = JSONArray.fromObject(result);
    resp.getWriter().write(jsonArray.toString());
  }
}
```



# JDBC

Java Database Connectivity是一个独立于特定数据库的管理系统，通用的SQL数据库存取和操作的公共接口。

定义了一组标准，为访问不同数据库提供了统一途径。

![javaweb02](/Users/anasiangangster/Desktop/Java培训/楠哥./picture//javaweb02.svg)

## JDBC体系结构

JDBC接口包括两个层面：

- 面向应用的API，供程序员调用
- 面向数据库的API，供厂商开发数据库的驱动程序

![javaweb03](/Users/anasiangangster/Desktop/Java培训/楠哥./picture//javaweb03.svg)

JDBC API

提供者：Java官方

内容：供程序员调用的接口

java.sql和javax.sql

- DriverManager类
- Connection接口
- Statement接口
- ResultSet接口

DriverManager

提供者：Java官方

作用：管理不同的JDBC驱动

JDBC驱动：

提供者：数据库厂商

作用：负责链接不同的数据库

## JDBC的使用

1. 加载数据库驱动（基站），Java程序和数据库之间的桥梁
2. 获取Connection（一次通话），Java程序与数据库的一次链接
3. 创建Statement对象（通话内容），由Connection产生，执行SQ语句
4. 如果需要接收返回值，创建ResultSet对象（通话内容），保存Statement执行之后所查询到的结果

```java
package com.anasiangangster.test;

import java.sql.*;
import java.util.Date;

public class Test {
  public static void main(String[] args) {
    try {
      //加载驱动-->加载到JVM内存，需要手动加载过来
      Class.forName("com.mysql.cj.jdbc.Driver");
      //获取链接
      String url = "jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8";
      String user = "root";
      String password = "password";
      Connection connection = DriverManager.getConnection(url, user, password);
      //            String sql = "insert into student(id, name, score, birthday) values(1,'张三',78,'1996-3-5')";
      //            String sql = "update student set name = '杨奥博'";
      //            String sql = "delete from student";
      //            Statement statement = connection.createStatement();
      //            int result = statement.executeUpdate(sql);
      //            System.out.println(result);
      String sql = "select * from student";
      Statement statement = connection.createStatement();
      ResultSet resultSet = statement.executeQuery(sql);
      while (resultSet.next()){
        Integer id = resultSet.getInt("id");
        String name = resultSet.getString(2);
        Integer score = resultSet.getInt(3);
        Date date = resultSet.getDate(4);
        System.out.println(id+"-"+name+"-"+score+"-"+date);
      }
    } catch (ClassNotFoundException | SQLException e) {
      e.printStackTrace();
    }
  }
}
```

## PreparedStatement

Statement的子类，提供了SQL语句占位符的功能。

使用Statement进行开发有两个问题：

1. 需要频繁拼接String字符串，出错率较高
2. 存在SQL注入的风险

SQL注入：利用某些系统没有对用户输入的信息进行充分的检测，在用户输入的数据中注入非法的SQL语句，从而利用系统的SQL引擎完成恶意行为的做法。

```java
String sql = "select * from t_user where username = ? and password = ?";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setString(1, username);
preparedStatement.setString(2, password);
ResultSet resultSet = preparedStatement.executeQuery();
```

