#Tomcat 配置
* **安装配置**：下载zip包后解压配置环境变量“CATALINA_HOME = D:\Android\OtherTool\apache-tomcat-8.0.24”-->JAVA_HOME如果没有配置也需要配置一下。-->starup.bat 双击就启动了。提示服务没有开启：service.bat install
* **如何共享目录**:tomcat默认访问的是webapps/ROOT目录
```
	<servlet>
	    <servlet-name>default</servlet-name>
	    <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
	    <init-param>
	        <param-name>debug</param-name>
	        <param-value>0</param-value>
	    </init-param>
	    <init-param>
	        <param-name>listings</param-name>
	    <param-value>true</param-value>
	    </init-param>
	    <load-on-startup>1</load-on-startup>
	</servlet>
```
* 配置tomcat使tomcat能找到我们的项目，在conf/server.xml找到倒数第四行在</Host>标签上加上下面的配置

```<Context path="/test" docBase="D:\git\MyEclipse Blue 2014\Test1\WebRoot" reloadable="true" />```

说明：/test 代表Tomcat访问的逻辑路径是/test， docBase代表的是我们项目的物理路径地址，也就是用户通过浏览器访问服务器资源的位置。reloadable意思是当我们在服务端编写代码(.java)后一般会自动的重启服务器。配置文件需要手动重启服务器
* 启动Tomcat服务器：startup.bat
* 访问:[http://localhost:8080/test/](http://localhost:8080/test/)

