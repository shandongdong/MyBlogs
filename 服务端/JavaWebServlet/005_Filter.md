# Filter（过滤器）
## 概念
- Servlet过滤器本身并不生成请求和响应对象， 它只提供过滤作用。Servlet过滤器负责过滤的Web组件可以是Servlet、 JSP或HTML文件。
- Servlet过滤器能够在Servlet被调用之前检查Request对象，修改Request Header和Request内容。
- Servlet过滤器能够在Servlet被调用之后检查Response对象，修改Response Header和Response内容。
## 过滤过程
![](http://cloudnotes.nos-eastchina1.126.net/20190220020821-531598.jpg)
## Filter接口
- 所 有 的 Servlet 过 滤 器 类 都 必 须 实 现javax.servlet.Filter接口。
- doFilter(ServletRequest request, ServletResponse response, FilterChain chain) 。doFilter方法完成实际的过滤操作。 当客户请求访问与过滤器关联的URL时， Servlet容器将先调用过滤器的doFilter方法。 FilterChain参数用于访问后续过滤器。
### FilterChain（过滤器链）
- Filters use the FilterChain to invoke the next filter in the chain, or if the calling filter is the last filter in the chain, to invoke the resource at the end of the chain. 
- ![](http://cloudnotes.nos-eastchina1.126.net/20190220022819-57997.jpg)
### 使用串联过滤器
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form method="get" action="../../../FilterChainServlet">
    username:<input type="text" name="username"><br/>
    <input type="submit" value="submit">

</form>
</body>
</html>
```
```java
@WebServlet(name = "FilterChainServlet", urlPatterns = "/FilterChainServlet")
public class FilterChainServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String userName = request.getParameter("username");
        System.out.println(userName);
    }
    @Override
    public void init() throws ServletException {
        System.out.println(this.getClass().getSimpleName() + " init invoked!!!");
    }

    @Override
    public void destroy() {
        System.out.println(this.getClass().getSimpleName() + " destroy invoked!!!");
    }
}

```
```java
@WebFilter(filterName = "FilterChainB", urlPatterns = "/FilterChainServlet")
public class FilterChainB implements Filter {
    public void destroy() {
        System.out.println(this.getClass().getSimpleName() + " destroy invoked!!!");
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println(this.getClass().getSimpleName() + " before doFilter invoked!!!");
        chain.doFilter(req, resp);
        System.out.println(this.getClass().getSimpleName() + " after doFilter invoked!!!");
    }

    public void init(FilterConfig config) throws ServletException {
        System.out.println(this.getClass().getSimpleName() + " init invoked!!!");
    }
}
```
```java
@WebFilter(filterName = "FilterChainC", urlPatterns = "/FilterChainServlet")
public class FilterChainC implements Filter {
    public void destroy() {
        System.out.println(this.getClass().getSimpleName() + " destroy invoked!!!");

    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        System.out.println(this.getClass().getSimpleName() + " before doFilter invoked!!!");
        chain.doFilter(req, resp);
        System.out.println(this.getClass().getSimpleName() + " after doFilter invoked!!!");
    }

    public void init(FilterConfig config) throws ServletException {
        System.out.println(this.getClass().getSimpleName() + " init invoked!!!");
    }
}
```
```
输出结果：
FilterChainC init invoked!!!
FilterChainB init invoked!!!

FilterChainServlet init invoked!!!
FilterChainB before doFilter invoked!!!
FilterChainC before doFilter invoked!!!
usernamexxx
FilterChainC after doFilter invoked!!!
FilterChainB after doFilter invoked!!!

FilterChainServlet destroy invoked!!!
FilterChainC destroy invoked!!!
FilterChainB destroy invoked!!!
```
- 需要注意的是如果使用@WebFilter注解来配置多个过滤器时**过滤器执行顺序与过滤器的urlPatterns的字母顺序有关**，可以通过修改“FilterChainC”名称为“FilterChainA”来看输出结果的不同。**init和destroy不太清除为什么没有按照顺序执行**，所以过滤器不建议使用@WebFilter的方式来配置。可在web.xml中通过配置<filter-mapping>标签，通过声明标签的先后来决定顺序的.
- 删除过滤器WebFilter注解，使用xml方式来指定顺序。（因为容器启动是会先加载xml文件，加载读读取xml文件使肯定知道编写的顺序，可以按照自己指定的顺序来加载过滤器）
```xml
<web-app>
    <display-name>Archetype Created Web Application</display-name>

    <filter>
        <filter-name>FilterChainB</filter-name>
        <filter-class>com.netease.recsys.test.study.servlet.filter.FilterChainB</filter-class>
    </filter>
    <filter>
        <filter-name>FilterChainC</filter-name>
        <filter-class>com.netease.recsys.test.study.servlet.filter.FilterChainC</filter-class>
    </filter>

    <!--filter-mapping决定执行顺序-->
    <filter-mapping>
        <filter-name>FilterChainC</filter-name>
        <url-pattern>/FilterChainServlet</url-pattern>
    </filter-mapping>
    <filter-mapping>
        <filter-name>FilterChainB</filter-name>
        <url-pattern>/FilterChainServlet</url-pattern>
    </filter-mapping>

</web-app>
```
```
输出结果：
FilterChainC init invoked!!!
FilterChainB init invoked!!!

FilterChainServlet init invoked!!!
FilterChainC before doFilter invoked!!!
FilterChainB before doFilter invoked!!!
usernamexxx
FilterChainB after doFilter invoked!!!
FilterChainC after doFilter invoked!!!

FilterChainC destroy invoked!!!
FilterChainB destroy invoked!!!
```
- 通过结果可以发现“doFilter invoked”方法的执行顺序改变了。



