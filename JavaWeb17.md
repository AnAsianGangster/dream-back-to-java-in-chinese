# 过滤器

Filter

熔断器Hystrix

功能：

1. 用来拦截传入的请求和传出的响应
2. 修改或以某中方式处理正在客户端和服务端之间交换的数据流

使用：

与使用Servlet类似、Filter是Java Web提供的一个接口，开发者只需要自定义一个类并且实现该接口即可

```java
package com.anasiangangster.filter;

import javax.servlet.*;
import java.io.IOException;

public class CharacterFilter implements Filter {
  @Override
  public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    servletRequest.setCharacterEncoding("UTF-8");
    filterChain.doFilter(servletRequest, servletResponse);
  }
}
```

web.xml中配置Filter

```xml
<filter>
  <filter-name>characterEncoding</filter-name>
  <filter-class>com.anasiangangster.filter.CharacterFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>characterEncoding</filter-name>
  <url-pattern>/login</url-pattern>
  <url-pattern>/test</url-pattern>
</filter-mapping>
```

注意：doFilter方法中处理完业务逻辑之后，必须添加

```java
filterChain.doFilter(servletRequest, servletResponse);
```

否则请求/响应无法向后传递，一直停留在过滤器中。

## Filter的生命周期

当Tomcat启动时，通过反射机制调用Filter的无参构造函数创建实例化对象，同时调用init方法实现初始化；

doFilter方法调用多次，当Tomcat服务关闭的时候，调用destroy来销毁Filter对象。

无参构造函数：只调用一次，当Tomcat启动时调用（Filter一定要进行配置）

init方法：只调用一次，当Filter的实例化对象创建完成之后调用

doFilter：调用多次，访问Filter的业务逻辑都写在Filter中

destroy：只调用一次，Tomcat关闭时调用

同时配置多个Filter，Filter的调用顺序是由web.xml中的配置顺序来决定的，写在上面的配置先调用，因为web.xml是从上到下顺序读取的。

```xml
<filter>
  <filter-name>characterEncoding</filter-name>
  <filter-class>com.anasiangangster.filter.CharacterFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>characterEncoding</filter-name>
  <url-pattern>/login</url-pattern>
  <url-pattern>/test</url-pattern>
</filter-mapping>

<filter>
  <filter-name>myFilter</filter-name>
  <filter-class>com.anasiangangster.filter.MyFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>myFilter</filter-name>
  <url-pattern>/login</url-pattern>
</filter-mapping>
```

顺序

1. CharacterFilter
2. MyFilter

也可以通过注解的方式来简化web.xml的中的配置

```java
@WebFilter("/login")
```

用注解不能决定顺序

实际开发中的过滤器的使用场景：

1. 统一处理中文乱码

2. 屏蔽敏感词

   ```java
   package com.anasiangangster.filter;
   
   import javax.servlet.*;
   import javax.servlet.annotation.WebFilter;
   import java.io.IOException;
   
   @WebFilter("/test")
   public class WordFilter implements Filter {
     @Override
     public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
       //将"敏感词"替换成"***"
       String name = servletRequest.getParameter("name");
       name = name.replaceAll("敏感词", "***");
       servletRequest.setAttribute("name", name);
       filterChain.doFilter(servletRequest, servletResponse);
     }
   }
   ```

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
       //        String name = req.getParameter("name");
       String name = (String) req.getAttribute("name");
       System.out.println(name);
     }
   }
   ```

3. 控制资源的访问权限

   ```java
   package com.anasiangangster.filter;
   
   import javax.servlet.*;
   import javax.servlet.annotation.WebFilter;
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import javax.servlet.http.HttpSession;
   import java.io.IOException;
   
   @WebFilter("/download.jsp")
   public class DownloadFilter implements Filter {
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           HttpServletRequest request = (HttpServletRequest) servletRequest;
           HttpServletResponse response = (HttpServletResponse) servletResponse;
           HttpSession session = request.getSession();
           String name = (String) session.getAttribute("name");
           if (name != null) {
               filterChain.doFilter(servletRequest, servletResponse);
           } else {
               //不是登录状态
               response.sendRedirect("/login.jsp");
           }
       }
   }
   ```

Filter拦截多个Servlet

```java
@WebFilter(urlPatterns = {"/test", "login"}) // "/*" 来实现全局
```

# 文件上传下载

- JSP
  1. input的type设置为file
  2. form表单的method设置为post（get请求会将文件名传给后端，而不是文件本身）
  3. form表单的enctype设置multipart/form-data，以二进制的形式传输数据。
  
- Servlet

  ```java
  package com.anasiangangster.servlet;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.*;
  
  @WebServlet("/upload")
  public class UploadServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //通过输入流获取客户端传来的数据流
      InputStream inputStream = req.getInputStream();
      Reader reader = new InputStreamReader(inputStream);
      BufferedReader bufferedReader = new BufferedReader(reader);
      //通过输出流将数据流出到本地硬盘
      //获取文件夹的绝对路径
      String path = req.getServletContext().getRealPath("file/copy.txt");
      OutputStream outputStream = new FileOutputStream(path);
      Writer writer = new OutputStreamWriter(outputStream);
      BufferedWriter bufferedWriter = new BufferedWriter(writer);
  
      String str = "";
      while ((str = bufferedReader.readLine()) != null) {
        bufferedWriter.write(str);
      }
  
      bufferedWriter.flush();
      bufferedWriter.close();
      writer.close();
      outputStream.close();
      bufferedReader.close();
      reader.close();
      inputStream.close();
    }
  }
  ```

  1. FileUpload组件可以将所有请求信息都解析成FileIterm对象，可以通过对FileItem对象的操作完成上传，面向对象的思想。

     ```java
     package com.anasiangangster.servlet;
     
     import org.apache.commons.fileupload.FileItem;
     import org.apache.commons.fileupload.FileUploadException;
     import org.apache.commons.fileupload.disk.DiskFileItemFactory;
     import org.apache.commons.fileupload.servlet.ServletFileUpload;
     
     import javax.servlet.ServletException;
     import javax.servlet.annotation.WebServlet;
     import javax.servlet.http.HttpServlet;
     import javax.servlet.http.HttpServletRequest;
     import javax.servlet.http.HttpServletResponse;
     import java.io.*;
     import java.util.List;
     
     @WebServlet("/upload")
     public class UploadServlet extends HttpServlet {
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         try {
           DiskFileItemFactory fileItemFactory = new DiskFileItemFactory();
           ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
           List<FileItem> list = servletFileUpload.parseRequest(req);
           for(FileItem fileItem : list) {
             if(fileItem.isFormField()) {
               String name = fileItem.getFieldName();
               String value = fileItem.getString("UTF-8");
               System.out.println("name = " + name);
               System.out.println("value = " + value);
             } else {
               String fileName = fileItem.getName();
               long size = fileItem.getSize();
               System.out.println(fileName+":"+size+"Bytes");
               InputStream inputStream = fileItem.getInputStream();
               String path = req.getServletContext().getRealPath("file/"+fileName);
               OutputStream outputStream = new FileOutputStream(path);
               int temp = 0;
               while ((temp = inputStream.read()) != -1) {
                 outputStream.write(temp);
               }
               outputStream.close();
               inputStream.close();
               System.out.println("上传成功");
             }
           }
         } catch (FileUploadException e) {
           e.printStackTrace();
         }
       }
     }
     ```

# 文件下载

```java
package com.anasiangangster.servlet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

@WebServlet("/download")
public class DownloadServlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    String type = req.getParameter("type");
    String fileName = "";
    switch (type) {
      case "png":
        fileName = "1.png";
        break;
      case "txt":
        fileName = "text.txt";
        break;
    }
    //设置响应的方式
    resp.setContentType("application/x-msdownload");
    //设置下载之后的文件名
    resp.setHeader("Content-Disposition", "attachment;filename="+fileName);
    //获取输出流
    OutputStream outputStream = resp.getOutputStream();
    String path = req.getServletContext().getRealPath("file/"+fileName);
    InputStream inputStream = new FileInputStream(path);
    int temp = 0;
    while ((temp = inputStream.read()) != -1) outputStream.write(temp);
    inputStream.close();
    outputStream.close();
  }
}
```

