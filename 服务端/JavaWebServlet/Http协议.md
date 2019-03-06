# Http 协议
## 协议介绍
- Http是一个基于请求/响应模式的，无状态的协议（request/response based, stateless protocol)。
    - 请求/响应：客户端向服务端发送请求，服务端返回响应数据。
    - 无状态：客户端第一次给服务端发送请求后，第二次给服务端有发送了一次请求，此时服务端并不知道这个请求时不是同一个客户端发过来了。（如何解决：服务端与客户端链接后通过唯一标识来区分是哪个客户端，比如session、token等机制）
- HTTP协议是基于TCP协议的。

### Http 1.1通讯过程
![](http://cloudnotes.nos-eastchina1.126.net/20181102032937-756488.jpg)
- HTTP URL
![](http://cloudnotes.nos-eastchina1.126.net/20181102033139-341655.jpg)

### 浏览器与服务器连接的一般过程
HTTP协议是基于TCP协议的，TCP协议要求必须先链接链接，所以HTTP协议首先要建立连接。TCP协议能保证建立连接后所有的请求和相应的顺序，所以HTTP协议建立链接后发送的请求和相应顺序不会乱。
```java
服务端建立连接等待客户端来连接
ServerSocket serverSocket = new ServerSocket(80);
serverSocket.accept();

客户端通过IP+Port号方式连接上指定主机，然后通过端口号来区分访问的是哪个程序
Socket socket = new Socket("127.0.0.1", 80);
```
## HTTP请求
### HTTP请求由三部分组成（浏览器封装）：
1. 请求行：以一个方法符号开头，后面跟着请求URI和协议的版本，以CRLF为结尾。请求行以空格分隔。除了作为结尾的CRLF外，不允许出现单独额CR（回车）或LF（换行）字符，格式如下：
    - Method Request-URI HTTP-Version CRLF
    - GET /test.html HTTP/1.1(CRLF)
2. 消息报头：
3. 请求正文:
### HTTP POST 方法
- POST方法要求服务器接受附在请求后面的多个数据。POST方法在表单提交的时候用的最多。
- POST 例子
```http
POST /login.jsp HTTP/1.1(CRLF)
Accept:image/gif(CRLF)(......)
HOST:www.sample.com(CRLF)(......)
......
......
Cache-Control:no-cache(CRLF)
(CRLF)
username=hello&password=world
```
## HTTP响应
与HTTP请求类似，HTTP响应也是由三个部分组成，分别是状态行、消息报头、响应正文。
- 状态行由协议版本，数字形式的状态代码，相应的状态描述组成，各元素之间以空格分隔，除了结尾的CRLF（回车换行）序列外，不允许出现CR或LF字符。格式如下：
> HTTP-Version Status-Code Reason-Phrase CRLF<br/>
> HTTP/1.1 200 OK (CRLF)

## 使用Telnet实验
- HTTP协议与Telnet协议都是基于TCP协议。
### 使用
由于telnet里面输入的东西是不能回显的，所以先在文本文档中准备数据。
1. cmd命令行输入：telnet www.sohu.com 80
2. 粘贴以下文本，获取响应头信息。（可以换成GET获取响应体）
```text
HEAD /server.html HTTP/1.1
Host: www.sohu.com
```
3. 敲两次回车，第一次回车代表消息到了结尾，第二次回车代表发送请求，HTTP协议格式要求。
4. HTTP协议默认是持续保持连接的，此时可以继续发送请求，过一会不发请求那么会自动断开。
![](http://cloudnotes.nos-eastchina1.126.net/20181102045803-365596.jpg)
- 例如：下面访问百度
```
telnet www.baidu.com 80
第一次获取请求头
HEAD /index.html HTTP/1.1
Host: www.baidu.com
第二次获取请求体
GET /index.html HTTP/1.1
Host: www.baidu.com
```
![](http://cloudnotes.nos-eastchina1.126.net/20181102050411-83007.jpg)

### 使用Tomcat实验
1. 下载Tomcat，配置CATALINA_HOME环境变量。
2. 使用telnet发送请求。
```telnet
telnet localhost 8080
第一次获取请求头信息
HEAD / HTTP/1.1
Host: localhost
第二次获取请求体信息
GET / HTTP/1.1
Host: localhost
```
3. 敲两次回车。
- Tomcat默认也是HTTP1.1协议，长链接方式，我们可以通过制定参数来控制链接。
例如：
```
GET / HTTP/1.1
Host: localhost
Connection:close
```
敲完两次回车后就直失去与主机的链接了。如果不指定此参数那么可以一直发送多个请求，都不会马上断开。
