# Servlet 入门
Servlet是JAVA服务器端编程，不同于之前写的一般的JAVA应用程序，Servlet程序是运行在服务器上的，服务器有很多种，这里用到的服务器就是Tomcat。
## 创建项目
> 参考文档“环境搭建Idea+Maven+tomcat8+JDK8+Servlet”
### 新建一个自己的页面
- 方式一：通过IDE(注意通过方式一没办法每次修改文件自动重新加载)
1. 在main/webapp/下新建一个first.html
2. 启动Tomcat后访问：http://localhost:8080/java-web-servlet/first.html
- 方式二：手工配置（推荐使用这种方式）
1. 配置好Tomcat、JDK的环境变量。
2. 在main/webapp/下新建一个first.html
3. 打开配置文件：.\apache-tomcat-8.5.30\conf\server.xml
4. 在倒数第四行“</Host>”标签的上一行添加"<Context path="/java-web-servlet" docBase="E:\git\java-web-servlet\src\main\webapp" reloadable="true" />"
参数说明：path是域名端口后的访问逻辑路径也叫上下文路径，自定义的。docBase为物料访问路径。reloadable=true表示修改后服务器自动重新加载，不设置的话那么每次修改都需要手动重启服务。当用户通过浏览器访问逻辑路径时，自动指向服务器的物理路径，然后服务器处理完后将结果返回给浏览器。
5. 启动tomcat：.\apache-tomcat-8.5.30\bin目录下双击“startup.bat”
6. 浏览器访问：http://localhost:8080/javawebservlet/first.html

### 创建Servlet实现HelloWorld
1. 在main目录上新建一个文件夹“java”，并且右键--make directory as-->Sources Root
2. 新建一个package
3. 在pom.xml添加Servlet依赖。
```xml
        <!-- Servlet 依赖jar包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
```
4. 创建类HelloWorldServlet extends HttpServlet
```java
public class HelloWorldServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        System.out.println("getRequestURL:" + req.getRequestURL());
//        System.out.println("getQueryString:" + req.getQueryString());

        resp.setContentType("text/html");
        PrintWriter out = resp.getWriter();
        out.println("<html><head><title>Hello world sample</title></head>");
        out.println("<body><h1>Hello World!!!welcome!!!!!!!!!!!!!!22233111111231232166666777123!!!</h1>");
        out.println(new Date().toLocaleString());
        out.println("</body></html>");
        
        out.flush();
    }
}
```
5. 修改web.xml文件
```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>HelloWorldServlet</servlet-name>
    <servlet-class>com.github.shandongdong.servlet.HelloWorldServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>HelloWorldServlet</servlet-name>
    <url-pattern>/HelloWorld</url-pattern>
  </servlet-mapping>

</web-app>

```
6. 在WEB-INF下创建classes文件夹
7. File-->Project Structure-->Modules-->Output path选择“E:\git\javawebservlet\src\main\webapp\WEB-INF\classes”
![](http://cloudnotes.nos-eastchina1.126.net/20181102101530-272618.jpg)
8. 启动服务
9. 访问：http://localhost:8080/java-web-servlet/HelloWorld
10. 遗留问题：每次修改java文件无法自动更新class到classes目录下，需要重启服务。（已解决：参考下面的# IDE配置）
- 上面HelloWorldServlet.java的执行流程
1. 用户通过浏览器访问http://localhost:8080/java-web-servlet/HelloWorld。
2. Tomcat的Server.xml中<Context path="/java-web-servlet" docBase="E:\git\java-web-servlet\src\main\webapp\" />发现存在此逻辑路径java-web-servlet,然后映射到具体的docBase路径。
3. 服务器查找部署文件web.xml中的servlet-mapping中的<url-pattern>发现存在资源/HelloWorld.
4. 查找servlet-mapping对应的servlet-name，然后实例化具体的java类文件。
5. 执行doGet方法。
6. 将执行结果返回给浏览器。

### 创建JSP实现HelloWorld
1. 在webapp目录下新建一个HelloWorld.jsp页面，内容如下：
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>HelloWorld</h1>
</body>
</html>
```
2. 启动服务器
3. 浏览器直接访问：http://localhost:8080/java-web-servlet/HelloWorld.jsp
4. 通过JSP方式无需配置web.xml。
### 在JSP中使用Java
- 语法：<% java.... %>
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head><title>Title</title></head>

<body>
<h1>HelloWorld:  <%= new java.util.Date().toLocaleString()%></h1>

</body>
</html>
```
- 通过上面的例子，我们可以将Servlet看做是嵌套的HTML代码的java类，可以将JSP看做是嵌套了java代码的HTML文件。

### 登录功能
1. 新建login.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="LoginServlet" method="get">
        username:<input type="text" name="username"> <br/>
        password:<input type="password" name="password"><br/>
        <input type="submit" value="submit">&nbsp;&nbsp;
        <input type="reset" value="reset">
    </form>
</body>
</html>
```
2. 新建LoginServlet.java
```java
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("------------------do post invoked!!!!-----------------");
        
        process(request, response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("------------------do get invoked!!!!-----------------");

        process(request, response);
    }

    private void process(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><head><title>Hello world sample</title></head>");
        out.println("<body><h1>");

        out.println("Hello world:");
        out.println("username: " + username);
        out.println("password: " + password);

        out.println("</h1></body></html>");
        out.flush();
    }
}
```
3. 编辑web.xml
```xml
    <servlet>
        <servlet-name>LoginServlet</servlet-name>
        <servlet-class>com.github.shandongdong.servlet.LoginServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>LoginServlet</servlet-name>
        <url-pattern>/LoginServlet</url-pattern>
    </servlet-mapping>
```
4. 启动服务器访问http://localhost:8080/java-web-servlet/login.jsp
5. 输入账号、密码-->提交。查看客户端结果及服务端打印语句。
6. 将login.jsp中 method="post"查看服务端打印。

### Get与POST 方法的差别
1. 浏览器呈现的结果不同（表象）。
2. 真正愿意在于向服务器发送的请求格式不同。
3. get方法请求格式：
```HTTP
Get /java-web-servlet/LoginServlet?username=aaa&password=bbb HTTP/1.1
```
4. post方法请求格式：
```HTTP
POST /java-web-servlet/LoginServlet HTTP/1.1


Connection:keep-Alive

username=aaa&password=bbb
```
请求行后面两个回车，然后参数作为请求体发送给服务器。
5. Get发送请求由长度限制，POST没有限制。

### 使用Servlet生产静态页面的流程
- 客户端、服务端与Servlet/JSP之间的关系。（客户端只会与服务器Tomcat交互，Tomcat接受到客户端的请求后与服务端Servlet进行交互，Servlet处理完后将结果返回给Tomcat，Tomcat将结果在返回给客户端。
![](http://cloudnotes.nos-eastchina1.126.net/20181106112737-738939.jpg)
### Servlet 在 Web 应用程序中的位置
![](http://cloudnotes.nos-eastchina1.126.net/20181106115021-759513.jpg)

### 与JSP的关系
Java服务器页面（JavaServer Pages）是HttpServlet的扩展。由于HttpServlet大多是用来响应HTTP请求，并返回Web页面（例如HTML、XML），所以不可避免地，在编写servlet时会涉及大量的HTML内容，这给servlet的书写效率和可读性带来很大障碍，JSP便是在这个基础上产生的。其功能是使用HTML的书写格式，在适当的地方加入Java代码片段，将程序员从复杂的HTML中解放出来，更专注于servlet本身的内容。
JSP在首次被访问的时候被应用服务器转换为servlet，在以后的运行中，容器直接调用这个servlet，而不再访问JSP页面。JSP的实质仍然是servlet。

### 使用注解的方式配置Servlet
1. 新建ServletAnnotation.java
```java
/**
 * 使用Servlet3.0新特性注解，配置Servlet，无需在web.xml里面配置。
 * name:            等价于 <servlet-name>。如果没有显式指定，则该 Servlet 的取值即为类的全限定名。
 * urlPatterns:     指定一组 Servlet 的 URL 匹配模式。等价于<url-pattern>标签。
 * loadOnStartup:   指定 Servlet 的加载顺序，等价于 <load-on-startup>标签。
 * initParams:      指定一组 Servlet 初始化参数，等价于<init-param>标签。
 */
@WebServlet(name = "TestServletAnnotation", urlPatterns = "/testAnnotation")
public class ServletAnnotation extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");

        PrintWriter printWriter = response.getWriter();

        printWriter.println("<html><head><title>Hello world sample</title></head>");
        printWriter.println("<body>");
        printWriter.println("<h1>");
        printWriter.println("This is Servlet Annotation test!!!");
        printWriter.println("</h1>");
        printWriter.println("</body></html>");

        printWriter.flush();
    }
}
```
2. 启动服务器
3. 访问“http://localhost:8080/java-web-servlet/testAnnotation”即可。无需配置web.xml。

### 使用JSP来处理客户端发送的请求
1. 新建login.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="result.jsp" method="post">
        username:<input type="text" name="username"> <br/>
        password:<input type="password" name="password"><br/>
        <input type="submit" value="submit">&nbsp;&nbsp;
        <input type="reset" value="reset">
    </form>
</body>
</html>
```
- <form action="result.jsp" method="post">这行代码表示用户通过浏览器将表单提交给相同目录下的“result.jsp”文件来处理请求。
2. 新建result.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<%
    String username = request.getParameter("username");
    String password = request.getParameter("password");

    out.println("username:" + username + "<br/>");
    out.print("password:" + password + "<br/>");
%>
</body>
</html>
```





























## IDE配置
### 配置classes输出目录
- File->Project Structure->Modules->Paths->
## 如何通过IDE自动更新java文件
- 在运行配置里面Server选项选择“Update classes and resources”，修改.java文件后无需重启服务端，会自动重新编译加载文件，参考图片.<br/>
![](http://cloudnotes.nos-eastchina1.126.net/20181105111358-382827.jpg)
![](http://cloudnotes.nos-eastchina1.126.net/20181105111333-749972.jpg)
> 参考：https://stackoverflow.com/questions/19596779/intellij-and-tomcat-changed-files-are-not-automatically-recognized-by-tomcat

### 打包发布项目
1. 命令行进入：E:\git\javawebservlet
2. cmd执行命令：mvn package
3. 将生成的“java-web-servlet.war”拷贝到Tomacat的".\apache-tomcat-8.5.30\webapps"目录下。
4. 启动服务“startup.bat”
5. 访问：http://localhost:8080/javawebservlet/HelloWorld


## 参考文档
> [Java Servlet](https://zh.wikipedia.org/wiki/Java_Servlet)<br/>
> [Servlet 教程](http://www.runoob.com/servlet/servlet-tutorial.html)<br/>
