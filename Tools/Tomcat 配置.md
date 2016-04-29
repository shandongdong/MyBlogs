#Tomcat 配置
* 安装配置：下载zip包后解压配置环境变量“CATALINA_HOME = D:\Android\OtherTool\apache-tomcat-8.0.24”-->JAVA_HOME如果没有配置也需要配置一下。-->starup.bat 双击就启动了。
提示服务没有开启：service.bat install
* 如何共享目录:tomcat默认访问的是webapps/ROOT目录
	1. 在ROOT目录下创建一个test目录
	2. 修改 conf/web.xml 文件的false为true即可
	```<servlet>
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
    </servlet>```
	3. [访问：http://localhost:8080/test/](http://localhost:8080/test/)
