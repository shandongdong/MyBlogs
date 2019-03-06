# Servlet 技术
## Servlet 介绍
- Java Servlet是和平台无关的服务器端组件，它运行在Servlet容器中。 Servlet容器负责Servlet和客户的通信以及调用Servlet的方法，Servlet和客户的通信采用“请求/响应”的模式。
- Servlet可完成如下功能：
    - 创建并返回基于客户请求的动态HTML页面。
    - 创建可嵌入到现有HTML 页面中的部分HTML页面（HTML 片段）。
    - 与其它服务器资源（如数据库或基于Java的应用程序）进行通信
- javax.servlet.Servlet：是一个独立的接口，我们没办法直接使用它，它定义了所有的Servlet程序必须要实现接口中的方法。通常我们写的Servlet都是继承自 javax.servlet.GenericServlet 或者一个基于HTTP协议的Servlet类javax.servlet.http.HttpServlet. 
- Servlet接口规定了如何初始化、移除等Servlet生命周期。它遵循以下顺序：
    - 1. 当Servlet初始化时会调用 init()方法。
    - 2. 任何从客户端发送过来的请求都会调用service()方法。
    - 3. 服务停止时会调用destroy()方法，并被JVM垃圾回收机制回收。
- HttpServlet：通常我们没有必须重写service()方法，service()方法是由容器（Tomcat）来调用的。通过客户端发送的每一个HTTP请求都会经过service()方法派发到指定协议方法的请求上。Servlet通常运行在一个多线程的服务器上，所以当我们在使用Servlet的时候必须小心访问共享资源，共享资源包含内存数据、对象、类成员变量、文件、数据库连接、网络连接。

## Servlet使用
### 访问Servlet
- 例子：通过浏览器发送Get请求访问Servlet程序
1. ServiceServlet.java
```java
@WebServlet(urlPatterns = "/ServiceServlet")
public class ServiceServlet extends HttpServlet {
    //service方法由容器来调用，然后根据客户端发送的协议来调用对应的方法
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("service invoked!!!");
        String username = req.getParameter("username");
        String password = req.getParameter("password");

        System.out.println("username=" + username + ", password=" + password);
    }
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet invoked!!!");

        String username = req.getParameter("username");
        String password = req.getParameter("password");

        System.out.println("username=" + username + ", password=" + password);
    }
}
```
2. http://localhost:8080/java-web-servlet/ServiceServlet?username=zhangsan&password=aaa
3. 查看服务端日志可以发现会先执行service方法。
- 为什么需要重写doXxx方法
1. DoGetServlet.java
```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }
```
2. 浏览器访问http://localhost:8080/java-web-servlet/DoGetServlet
3. 结果：“HTTP Status 405 – Method Not Allowed”
4. 默认调用父类的doXxx方法Servlet容器会返回一个错误页面，所以我们需要重写doGet()、doPost()。。。等方法。

### 小结
1. 每一个Servlet都必须要实现Servlet接口，GenericServlet 是个通用的，不特定于任何协议的Servlet，它实现了Servlet接口，而HttPServlet继承于GenericServlet，因此 HttPServlet也实现了Servlet接口，所以我们定义的Servlet只需要继承 HttPServlet 父类即可。
2. Servlet 接口中定义了一个service()方法，HttPServlet对其进行了实现，实现方式就是将 ServletRequest 和 ServletResponse 转换为HttPServletRequest 与 HttPServletResponse。
```java
    @Override
    public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException
    {
        HttpServletRequest  request;
        HttpServletResponse response;
        
        if (!(req instanceof HttpServletRequest &&
                res instanceof HttpServletResponse)) {
            throw new ServletException("non-HTTP request or response");
        }

        request = (HttpServletRequest) req;     //转换为HttpServletRequest
        response = (HttpServletResponse) res;

        service(request, response);
    }
```
3. 转换完后会，会调用 HttpServlet 类中自己定义的 service 方法。这个方法接受标准的HTTP协议请求，并且将请求转发到这个类中的doXxx()方法上执行。
```java
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String method = req.getMethod();    // step1:获取请求的方法名
        long lastModified;
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                this.doGet(req, resp);
            } else {
                long ifModifiedSince = req.getDateHeader("If-Modified-Since");
                if (ifModifiedSince < lastModified) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }

    }
```
4. 在该方法中，首先会获得请求名，然后根据方法名调用对应的doXxx方法。比如请求方法为GET，那么就会调用doGet()方法，请求方法为POST那么就会调用doPost()方法。
5. 在HttPServlet类中提供的doXxx方法都是直接返回错误信息，所以我们需要在自己定义的Servlet类中override这些方法。
```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
        throws ServletException, IOException
    {
        String protocol = req.getProtocol();
        String msg = lStrings.getString("http.method_get_not_supported");
        if (protocol.endsWith("1.1")) {
            resp.sendError(HttpServletResponse.SC_METHOD_NOT_ALLOWED, msg);     //返回客户端一个错误页面
        } else {
            resp.sendError(HttpServletResponse.SC_BAD_REQUEST, msg);
        }
    }
```
## Servlet API
### Servlet生命周期
- init方法：负责初始化Servlet对象；
- service方法：负责响应客户的请求；
- destroy方法：当Servlet对象退出生命周期时， 负责释放占用的资源。

#### Servlet容器装载Servlet时机
- Servlet容器启动时自动装载某些Servlet
- 在Servlet容器启动后，客户首次向 Servlet 发出请求
- Servlet的类文件被更新后，重新装载Servlet

#### 初始化init()方法
- Servlet被装载后，Servlet容器创建一个Servlet实例并且调用Servlet的init()方法进行初始化。**在Servlet的整个生命周期中，init方法只会被调用一次**。
1. 新建InitServlet.java
```java
@WebServlet(urlPatterns = "/InitServlet")
public class InitServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("doGet invoked!!!!!");
    }

    @Override
    public void init() throws ServletException {
        System.out.println("init invoked!!!");
    }
}
```
2. 第一次访问：http://localhost:8080/java-web-servlet/InitServlet
```html
第一次访问结果
init invoked!!!             //第一次启动自动调用
doGet invoked!!!
第二次刷新浏览器
doGet invoked!!!
第三次修改InitServlet.java后服务器自动重新编译class文件后访问浏览器
init invoked!!!             //修改java文件重新编译class文件自动调用
doGet invoked!!!
第四次刷新浏览器
doGet invoked!!!
```
3. 配置Servlet为**自动装载**“@WebServlet(urlPatterns = "/InitServlet", loadOnStartup = 7)”。loadOnStartup用于指定Servlet启动的顺序。
4. 重启服务器发现init()方法被自动状态调用。
### Servlet核心API
![](http://cloudnotes.nos-eastchina1.126.net/20181115064355-26311.jpg)


### getParameter()方法
1. 对于tomcat来说，他会将传递过来的参数放到一个Map<String,ArrayList<String>>中，改Map的定义是：
```java
package org.apache.tomcat.util.http;
    private final Map<String,ArrayList<String>> paramHashValues =
            new LinkedHashMap<>();
```
2. 当我们调用HttpServletRequest.getParameter()时，会调用javax.servlet.ServletRequestWrapper.getParameter()方法，改方法中定义了一个Request对象。
```java
    public String getParameter(String name) {
        return this.request.getParameter(name);
    }
```
3. 这个Request对象是Tomcat实现的org.apache.catalina.connector.Request
```java
    /**
     * Hash map used in the getParametersMap method.
     */
    protected ParameterMap<String, String[]> parameterMap = new ParameterMap<>();
```
4. 继续查找“getParameter()”方法
```java
    public String[] getParameterValues(String name) {
        handleQueryParameters();
        // no "facade"
        ArrayList<String> values = paramHashValues.get(name);
        if (values == null) {
            return null;
        }
        return values.toArray(new String[values.size()]);
    }
```
5. 这里会调用handleQueryParameters()方法进行请求参数的解析。
6. 解析完请求参数后会调用addParameter(name, value);
7. 在此方法中会调用ArrayList<String> values = paramHashValues.get(key);
8. values.add(value);
9. paramHashValues引用最终存储了客户端发送过来的请求参数键值对，改引用是org.apache.tomcat.util.http.Parameters类中定义的Map<String,ArrayList<String>> paramHashValues
```java
private final Map<String,ArrayList<String>> paramHashValues =
            new LinkedHashMap<>();
```
#### 销毁destroy()方法
- 当Web应用被终止，或Servlet容器终止运行，或Servlet容器重新装载Servlet的新实例时， Servlet容器会先调用 Servlet的destroy方法。在destroy方法中，可以释放Servlet所占用的资源。
```java
    @Override
    public void destroy() {
        System.out.println("修改后会重新调用此方法。");
        System.out.println("destroy invoked!!");
    }
```
### Servlet对象的创建
- 默认情况下，当Web客户第一次请求访问某个Servlet时， Web容器创建这个Servlet的实例。如果设置了<servlet>元素的<load-on-startup>子元素， Servlet容器在启动Web应用时，将按照指定的顺序创建并初始化这个Servlet。
- 某些Servlet在 web.xml 中只有<servlet>元素没有<servlet-mapping>元素，这样我们就无法通过url访问这个Servlet了，这种Servlet通常会在<servlet>元素中配置一个<load-on-startup>子元素，让容器在启动的时候自动加载该Servlet，并且调用其init()方法完成一些全局的初始化工作。

### ServletContext使用
- 当Servlet容器启动Web应用时，并为每个Web应用创建惟一的ServletContext对象。你可以把ServletContext看成是一个Web应用的服务器端组件的共享内存。
1. ServletCounter.java
```java
// 使用ServletContext实现jsp中的application对象功能
@WebServlet(name = "ServletCounter", urlPatterns = "/ServletCounter")
public class ServletCounter extends HttpServlet {

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext servletContext = request.getSession().getServletContext();
        if (null == servletContext.getAttribute("counter")) {
            servletContext.setAttribute("counter", 1);
        } else {
            int counter = (Integer) servletContext.getAttribute("counter");
            servletContext.setAttribute("counter", counter + 1);
            counter++;
        }
        request.getRequestDispatcher("counter.jsp").forward(request, response);
    }
}
```
2. counter.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
计数：<%= application.getAttribute("counter")%>
</body>
</html>
```
## Servlet 多线程
- Servlet/JSP默认是以多线程模式执行的，所以，在编写代码时需要非常细致地考虑多线程的同步问题。
1. form.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="HelloServlet">
    username:<input type="text" name="username">
    <input type="submit"  value="submit">
</form>
</body>
</html>
```
2. HelloServlet.java
```java
@WebServlet(name = "HelloServlet", urlPatterns = "/HelloServlet")
public class HelloServlet extends HttpServlet {
    private String username;
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.username = request.getParameter("username");

        try {
            // process business
            Thread.sleep(10000);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        request.setAttribute("username", username);
        request.getRequestDispatcher("world.jsp").forward(request, response);
    }
}
```
3. world.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%= request.getAttribute("username") %>
</body>
</html>
```
4. 打开两个浏览器同时访问form.jsp，输入两个不用的用户名然后在10秒内两个都提交，会发现后提交的会覆盖之前的。
5. 原因：Servlet是单实例的，我们在类里面定义的username是成员变量，所以所有访问HelloServlet的请求都会共享这个实例，后访问的当然会对之前访问的产生影响。
6. 解决Servlet多线程同步问题的最好方案：去除实例变量，使用局部变量。

## Cookie
- Cookie是用户在访问服务器时，服务器在用户硬盘（或浏览器进程中，例如sessionID）上存放的信息。一个Cookie包含一对key/value。
- Java中使用cookie直接使用javax.servlet.http.Cookie类。Cookie是在服务端生成的，可以通过response对象发送给客户端。客户端可以通过request对象拿到本地的cookie然后在发送给服务端。
```java
@WebServlet(name = "ServletCookie", urlPatterns = "/ServletCookie")
public class ServletCookie extends HttpServlet {

    private int count1;
    private int count2;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        Cookie cookie = new Cookie("cookiesName" + count1++, "cookiesValue" + count2++);

        cookie.setMaxAge(20);       //设置Cookie存活的时间，单位秒。-1表示永不过期

        response.addCookie(cookie);     //要想将cookie保存到客户端只能通过响应对象

        //cookie是存在客户端硬盘上的，所以如果要拿到只能通过请求对象获取
        Cookie[] cookies = request.getCookies();

        if (cookies == null) return;
        for (Cookie c : cookies) {
            System.out.println("cookiesName:" + c.getName());
            System.out.println("cookiesValue:" + c.getValue());
        }
    }
}
```
