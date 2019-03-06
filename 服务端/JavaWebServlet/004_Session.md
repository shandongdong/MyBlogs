# Session(会话)
## Session 概念
- Session用于跟踪客户的状态。Session指的是在一段时间内，单个客户与Web服务器的一连串相关的交互过程。在一个Session中，客户可能会多次请求访问同一个网页，也有可能请求访问各种不同的服务器资源。
## Session的运行机制
- 当一个Session开始时，Servlet容器将创建一个HttpSession对象，在HttpSession对象中可以存放客户状态的信息（例如购物车） 。
- Servlet容器为HttpSession分配一个惟一标志符，称为SessionID。Servlet容器把SessionID作为Cookie保存在客户的浏览器中。
- 每次客户发出HTTP请求时，Servlet容器可以从HttpServletRequest对象中读取SessionID，然后根据SessionID找到相应的HttpSession对象，从而获取客户的状态信息。
- ![](http://cloudnotes.nos-eastchina1.126.net/20190214035643-559773.jpg)
- ![](http://cloudnotes.nos-eastchina1.126.net/20190214040701-334131.jpg)
> 客户端1发送请求到容器-->容器创建HTTPSession对象和SessionID-->通过rsponse.addCookie(cookie)方式将SessionID返回客户端。客户端1第二次发送请求时携带SessionID-->此时服务器判断是否存在此SessionID对象，如果存在那么客户端1发送的请求则交给对应的SessionID对象进行数据的处理。
```java
@WebServlet(name = "ServletSession", urlPatterns = "/ServletSession")
public class ServletSession extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        HttpSession session = request.getSession();

        System.out.println(session.getId());

        System.out.println(session.isNew());

        session.setMaxInactiveInterval(30); //设置 session的有效时间-

        session.invalidate();   //设置session失效
    }
}
```
## Session 生命周期
### Session生命周期结束的两种方法
1. session过期
2. 服务器调用了HttpSession的invalidate()方法。























