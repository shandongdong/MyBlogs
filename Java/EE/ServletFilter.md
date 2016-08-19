# Servlet Filter（过滤器）
## 概念
    能够对Servlet容器的请求和响应对象进行检查和修改。
## 作用
1. Servlet 过滤器本身并不生产请求和响应对象，它只提供过滤作用；
2. Servlet 过滤器能够在Servlet被调用之前检查Request对象，修改Request Header和Request 内容；
3. 在Servlet被调用之后检查Response对象，修改Response Header和Response内容。

### Servlet 过滤器的过滤过程
![Servlet 过滤器的过滤过程](https://github.com/shandongdong/MyBlogs/blob/master/Java/Pictrue/Servlet%E8%BF%87%E6%BB%A4%E5%99%A8%E7%9A%84%E8%BF%87%E6%BB%A4%E8%BF%87%E7%A8%8B.png?raw=true)
### Filter接口
* 所有的Servlet过滤器类都必须实现javax.servlet.Filter接口。实现了这个接口的java类就称为过滤器。这个接口包含3个过滤类必须实现的方法：
    1. init(FilterConfig filterConfig):过滤器初始化方法，Servlet容器创建Servlet过滤器实例后将调用这个方法。在这个方法中可以读取web.xml 文件中Servlet过滤器的初始化参数。
    2. doFilter(ServletRequest request, ServletResponse response, FilterChain chain) ：这个方法完成实际的过滤操作。当客户请求访问与过滤器关联的URL时，Servlet容器将先调用过滤器的doFilter()方法。FilterChiain参数用于访问后续过滤器。
    3. destory()：Servlet容器在销毁过滤器实例前调用该方法。
其中最重的是doFilter()方法，该方法中的FilterChain过滤链的含义是“Filters use the FilterChain to invoke the next filter in the chain, or if the calling filter is the last filter in the chain, to invoke the resource at the end of the chain”
![过滤器的链式请求过程FilterChain](https://github.com/shandongdong/MyBlogs/blob/master/Java/Pictrue/%E8%BF%87%E6%BB%A4%E5%99%A8%E9%93%BE%E4%BD%9C%E7%94%A8.png?raw=true)


