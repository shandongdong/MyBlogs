# Listerner
## 概念
- Listener是Servlet的监听器，它可以监听客户端的请求、服务端的操作等。很多框架的底层就是通过listener方式来实现。
```
package com.netease.recsys.test.study.servlet.listener;

import javax.servlet.ServletContextAttributeEvent;
import javax.servlet.ServletContextAttributeListener;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;
import javax.servlet.http.HttpSessionAttributeListener;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;
import javax.servlet.http.HttpSessionBindingEvent;

@WebListener()
public class MyServletContextListener implements ServletContextListener,
        HttpSessionListener, HttpSessionAttributeListener, ServletContextAttributeListener {

    public void contextInitialized(ServletContextEvent sce) {
        System.out.println(this.getClass().getSimpleName() + " initialized invoked!!! " + sce.getServletContext());
    }

    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println(this.getClass().getSimpleName() + " destroyed invoked!!! " + sce.getServletContext());
    }

    public void sessionCreated(HttpSessionEvent se) {
        System.out.println(this.getClass().getInterfaces() + " sessionCreated invoked!!!");
    }

    public void sessionDestroyed(HttpSessionEvent se) {
        System.out.println(this.getClass().getSimpleName() + " sessionDestroyed invoked!!!");
    }

    public void attributeAdded(HttpSessionBindingEvent se) {
        System.out.println("attribute added");
        System.out.println(se.getName() + ":" + se.getValue());
    }

    public void attributeRemoved(HttpSessionBindingEvent se) {
        System.out.println("attribute removed");
        System.out.println(se.getName() + ":" + se.getValue());
    }

    public void attributeReplaced(HttpSessionBindingEvent se) {
        System.out.println("attribute replaced");
        System.out.println(se.getName() + ":" + se.getValue());
    }

    public void attributeAdded(ServletContextAttributeEvent scab) {
        System.out.println("attribute added");
        System.out.println(scab.getName() + ":" + scab.getValue());
    }

    public void attributeRemoved(ServletContextAttributeEvent scab) {
        System.out.println("attribute removed");
        System.out.println(scab.getName() + ":" + scab.getValue());
    }

    public void attributeReplaced(ServletContextAttributeEvent scab) {
        System.out.println("attribute replaced");
        System.out.println(scab.getName() + ":" + scab.getValue());
    }
}

```
- ServletContextListener监听器的使用
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
<%
    if (application.getAttribute("counter") == null) {
        application.setAttribute("counter", "1");
        System.out.println("if");
    } else {
        String str = application.getAttribute("counter").toString();
        int i = Integer.valueOf(str).intValue();
        i++;
        application.setAttribute("counter", Integer.toString(i));
        System.out.println("else");
    }
%>
您是第<%= application.getAttribute("counter") %> 位访问者!
</body>
</html>
```
- listener1.jsp
```html
<body>

<% application.setAttribute("aa", "bb"); %>

<% application.setAttribute("aa", "cc"); %>

<% application.setAttribute("aa", "dd"); %>

<% application.setAttribute("aa", "ee"); %>

</body>
```
- listener2.jsp
```html
<body>
<% application.removeAttribute("aa"); %>
</body>
```
## EL(Expression Language)表达式语言
- EL目的让jsp中消除Java语言。

## 自定义标签
### 创建标签的步骤
1. 创建标签的处理类。
2. 创建标签描述文件。
3. 在JSP文件中引入标签库，然后插入标签。例如：<mm:hello/>



