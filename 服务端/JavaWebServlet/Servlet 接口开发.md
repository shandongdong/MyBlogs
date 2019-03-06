# Servlet 接口开发
## 开发Get接口
- 接口：http://localhost:8080/java-web-servlet/api/v1/assert?byte=127&short=123&int=111&object=User
1. 新建“AssertType.java"
```java
   @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("------------------do get invoked!!!!-----------------");
        System.out.println(request.getRequestURL().toString());
        System.out.println(request.getQueryString());
        System.out.println("请求路径是：" + request.getRequestURL() + "?" + request.getQueryString());
        System.out.println("---------------------Map start--------------------------");
        Map<String, String[]> map1 = request.getParameterMap();
        for (Map.Entry<String, String[]> entry : map1.entrySet()) {
            System.out.println("key = " + entry.getKey() + ", value = " + entry.getValue()[0]);
        }
        System.out.println("-------------------------map end----------------------------");
        System.out.println("-------------------------enum start------------------------");
        Enumeration<String> enumeration = request.getParameterNames();
        while (enumeration.hasMoreElements()) {
            String key = enumeration.nextElement();
            String value = request.getParameter(key);
            System.out.println("key = " + key + ", value = " + value);
        }
        System.out.println("--------------------------enum end ------------------------");

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><head><title>Hello interface sample</title></head>");
        out.println("<body><h1>");
        out.println("interface access success!!!");
        out.println("</h1></body></html>");

        out.flush();
    }
```
2. web.xml配置
```xml
    <servlet>
        <servlet-name>AssertType</servlet-name>
        <servlet-class>com.github.shandongdong.servlet.asserts.AssertType</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>AssertType</servlet-name>
        <url-pattern>/api/v1/assert</url-pattern>
    </servlet-mapping>    
```
3. 启动服务器，使用postman访问http://localhost:8080/java-web-servlet/api/v1/assert?byte=127&short=123&int=111&object=User&json={"username":"tester"}&jsonArray="[{"username:"tester"}]"&arraySize="[{"username:"tester"}]"
4. 查看服务器执行结果
```java
------------------do get invoked!!!!-----------------
http://localhost:8080/java-web-servlet/api/v1/assert
byte=127&short=123&int=111&object=User&json=%7B%22username%22:%22tester%22%7D&jsonArray=%22[%7B%22username:%22tester%22%7D]%22&arraySize=%22[%7B%22username:%22tester%22%7D]%22
请求路径是：http://localhost:8080/java-web-servlet/api/v1/assert?byte=127&short=123&int=111&object=User&json=%7B%22username%22:%22tester%22%7D&jsonArray=%22[%7B%22username:%22tester%22%7D]%22&arraySize=%22[%7B%22username:%22tester%22%7D]%22
---------------------Map start--------------------------
key = byte, value = 127
key = short, value = 123
key = int, value = 111
key = object, value = User
key = json, value = {"username":"tester"}
key = jsonArray, value = "[{"username:"tester"}]"
key = arraySize, value = "[{"username:"tester"}]"
-------------------------map end----------------------------
-------------------------enum start------------------------
key = byte, value = 127
key = short, value = 123
key = int, value = 111
key = object, value = User
key = json, value = {"username":"tester"}
key = jsonArray, value = "[{"username:"tester"}]"
key = arraySize, value = "[{"username:"tester"}]"
--------------------------enum end ------------------------
```
5. 服务器可以获取请求的所有参数，然后做对应的业务处理。
6. 请求参数中如果有特殊字符那么需要编码后才能提交给服务器。可以使用postman提交，如果是无特殊字符直接使用浏览器即可。
7. 