# JSP内置对象作用域

4个作用域

page、request、session、application

都有setAttribute、getAttribute方法

page作用域：对应的内置对象是pageContext

request作用域：对应的内置对象是request

session作用域：对应的内置对象是session

application作用域：对应的内置对象是application

page < request < session < application

page只在当前页面有效

request在一次请求内有效

session在一次会话内有效

application对应整个Web应用

> 重定向(redirect)是叫客户端重新发一次请求，请求转发(forward)还是同一个请求

- 网站的访问量统计

  ```jsp
  <%
  	Integer count = (Integer) application.getAttribute("count");
  	if (count == null) {
    	count = 1;
    	application.setAttribute("count", count);
  	} else {
    	count = (Integer) application.getAttribute("count");
    	count++;
    	application.setAttribute("count", count);
  	}
  %>
  <%="你是当前的第"+count+"位访客"%>
  ```

# EL表达式

Expression Language 表达式语言，替代JSP页面中数据访问时的复杂编码。可以非常便捷地取出域对象（pageContext、request、session、application）中保存的数据，前提是一定要先setAttribute，EL就相当于在简化getAttribute

${变量名}变量名就是setAttribute对应的key值

1. EL对应4中域对象的默认查找顺序：

   pageContext -> request -> session -> application

   按照上述的顺序进行查找，找到立即返回，在application中也无法找到，则返回null

2. 指定作用域进行查找

   pageContext：${pageScope.name}

   request：${requestScope.name}

   session：${sessionScope.name}

   application：${application.name}

数据级联

```jsp
    <%
//        pageContext.setAttribute("name", "page");
//        request.setAttribute("name", "request");
//        session.setAttribute("name", "session");
//        application.setAttribute("name", "application");
        User user = new User(1, "张三", 25);
        pageContext.setAttribute("user", user);
    %>
<%--    ${pageScope.user}--%>
    <table>
        <tr>
            <th>编号</th>
            <th>姓名</th>
            <th>年龄</th>
        </tr>
        <tr>
            <td>${user.setId(34)}</td>
            <td>${user.getName()}</td>
            <td>${user.age}</td>
        </tr>
    </table>
    ${user}
		${user["age"]}
```

EL执行表达式

与、或、非；&&、｜｜、！<、>、<=、>=、==

```shell
&& and
|| or
! not
== eq
!= ne
< lt
> gt
<= le
>= ge
empty 变量为null，长度为0的String，size为0的集合
```

# JSTL

JSP Standard Tag Library JSP标准标签库，JSP为开发者提供的一系列的标签，使用这些标签可以完成一些逻辑处理，比如循环遍历集合，让代码更加简洁，不再出现JSP脚本穿插的情况。

实际开发中EL和JSTL结合起来使用，JSTL侧重于逻辑处理，EL负责展示数据

JSTL的使用

1. 需要导入jar包，（两个jstl.jar standard.jar）手动导入Web-INFO/lib

2. 在JSP页面开始的地方导入JSTL标签库

   ```jsp
   <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
   ```

3. 在需要的地方使用

   ```jsp
   <c:forEach var="user" items="${list}">
     <tr>
       <td>${user.id}</td>
       <td>${user.name}</td>
       <td>${user.age}</td>
     </tr>
   </c:forEach>
   ```

JSTR的优点：

1. 提供了统一的标签
2. 可以用于编写各种动态功能

## 核心常用标签

set、out、remove、catch

set：向域对象中添加数据

```jsp
<%
	request.setAttribute(key, value)
%>
${name}

<c:set var="name" value="tom" scope="request"></c:set>
${requestScope.name}

<%
User user = new User(1, "aobo", 25);
request.setAttribute("user", user);
%>
<c:set target="${user}" property="name" value="aobo2"></c:set>
${user.name}
```

out：输出域对象中的数据

```jsp
<c:set var="name" value="tom"></c:set>
<c:out value="${name}" default="未定义"></c:out>
```

remove：删除域对象中的数据

```jsp
<c:remove var="name" scope="page"></c:remove>
```

catch：捕获异常

```jsp
<c:catch var="error">
  <%
  int a = 10 / 0;
  %>
</c:catch>
${error}
```

条件标签：

if、choose

```jsp
<c:set var="num1" value="1"></c:set>
<c:set var="num2" value="2"></c:set>
<c:if test="${num1 > num2}">ok</c:if>
<c:if test="${num1 < num2}">fail</c:if>
<hr>
<c:choose>
  <c:when test="${num1 > num2}">ok</c:when>
  <c:otherwise>fail</c:otherwise>
</c:choose>
```

迭代标签：

forEach

```jsp
<%
	List<String> list = new ArrayList<>();
	list.add("a");
	list.add("b");
	list.add("c");
	list.add("d");
	list.add("e");
	list.add("f");
	list.add("g");
	list.add("h");
	list.add("i");
	request.setAttribute("list", list);
%>
<c:forEach var="character" items="${list}" begin="2" end="3" step="2" varStatus="status">
  ${status.index}-${character}<br>
</c:forEach>
```

## 格式化标签库常用的标签

```jsp
<%
	request.setAttribute("date", new Date());
%>
<fmt:formatDate value="${date}" pattern="yyyy-MM-dd HH:mm:ss"></fmt:formatDate>
<fmt:formatNumber value="5640640.68046840" maxIntegerDigits="2" maxFractionDigits="3"></fmt:formatNumber>
```

## 函数标签库常用的标签

```JSP
<%
	request.setAttribute("info", "Java,C");
%>
${fn:contains(info,"python")}<br>
${fn:startsWith(info,"Java")}<br>
${fn:endsWith(info,"C")}<br>
${fn:indexOf(info,"va")}<br>
${fn:replace(info,"C","Python")}<br>
${fn:substring(info,2,3)}<br>
${fn:split(info, ",")[0]}
```

