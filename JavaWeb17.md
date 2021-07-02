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