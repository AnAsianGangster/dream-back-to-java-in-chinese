# Session

用户会话

服务器无法识别每一次HTTP请求的出处（不知道来自于哪个终端），它只会接收到一个请求信号，所以说就存在一个问题：将用户的响应发送给其他人必须有一种技术来让服务器知道请求来自哪，这就是会话技术。

会话：就是客户端和服务器之间发生的一系列连续的请求和响应的过程，打开浏览器进行操作到关闭浏览器的过程。

会话状态：指服务器和浏览器在会话过程中过产生的状态信息，借助于会话的状态，服务器能够把属于同一次会话的一系列请求和响应关联起来。

实现会话有两种方式：

- session 服务端
- cookie 客户端

属于同一次会话的请求都有一个相同的标识符，sessionID

## Session常用方法

String getId() 获取sessionID

void setMaxInactiveInterval(int interval) 设置session的失效时间的，单位为秒

int getMaxInactiveInterval() 获取当前的session失效时间

void invalidate() 设置session立即失效

void setAttribute(String key, Object value) 通过键值对的形式来存储数据

Object getAttribute(String key) 通过键获取对应的数据

void removeAttribute(String key) 通过键删除对应的数据

# Cookie

Cookie是服务端在HTTP响应中附带传给浏览器的一个小的文本文件，一旦浏览器保存了某个Cookie，在之后的请求和响应过程中，会将次Cookie来回传递，这样就可以通过Cookie这个载体完成客户端和服务端的数据交互。

Cookie类

- 创建Cookie

  ```jsp
  <%
  	Cookie cookie = new Cookie("name", "aobo");
  	response.addCookie(cookie);
  %>
  ```

- 读取Cookie

  ```java
  Cookie[] cookies = request.getCookies();
  for (Cookie cookie:cookies) {
    System.out.println(cookie.getName());
  }
  ```

Cookie常用方法

void setMaxAge(int age) 设置Cookie有效时间，单位为秒

int getMaxAge() 获取Cookie的有效时间

String getName() 获取Cookie的name

String getValue() 获取Cookie的value

## Session和Cookie的区别

session：

- 保存在服务器（JVM内存）

- 保存的数据是Object
- 会随着会话的结束而销毁
- 保存重要信息

cookie：

- 保存在浏览器

- 保存的数据是String
- 可以长期保存在浏览器中，与会话无关
- 保存不重要信息



存储用户信息：

session：

- setAttribute("name", "admin") 存
- getAtrribute("name") 取
- 生命周期：服务端，只要Web应用重启就销毁；客户端，只要浏览器关闭就销毁
- 退出登录：session.invalidate()

cookie：

- response.addCookie(new Cookie("name", "admin")) 存

- ```jsp
  <%
  	Cookie[] cookies = request.getCookies();
  	for (Cookie cookie: cookies) {
    	if (cookie.getName().equals("name")) {
  %>
  			<%=cookie.getName()+":"+cookie.getValue()%>
  <%
  		}
  	}
  %>
  <a href="/cookielogout">退出登录</a>
  ```

  取

- 生命周期：不随服务端的重启而销毁，客户端：默认是只要关闭浏览器就销毁，我们通过setMaxAge()方法设置有效期，一旦设置了有效期，则不随浏览器的关闭而销毁，而是由设置的时间来决定。
- 退出登录：setMaxAge(0)
