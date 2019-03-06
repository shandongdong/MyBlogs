# 环境搭建Idea+Maven+tomcat9.0.12+JDK8+Servlet
## 安装
略
## 配置
### JDK配置
- JAVA_HOME：D:\Program Files\Java\jdk1.8.0_162
- CLASS_PATH：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
- path：%JAVA_HOME%\bin;
### Tomcat配置
1. 计算机右击属性，打开环境变量---系统变量---新建变量名：CATALINA_BASE，变量值为：E:\software\apache-tomcat-8.5.30
2. 新建变量名：CATALINA_HOME，变量值为：E:\software\apache-tomcat-9.0.12
3. 打开PATH，添加变量值：%CATALINA_HOME%\lib; %CATALINA_HOME%\bin;
4. CMD命令下输入命令：startup.
5. 浏览器地址栏中输入http://localhost:8080后回车，如果看到Tomcat自带的一个“猫”界面，说明你的JDK和Tomcat已搭建成功。

### Idea 创建创建Servlet项目
由于IDEA社区版（Community）无法直接New一个Web Appplication 所以要使用maven来创建。
1. File-new-project-maven,勾选"create from archetype"
2. 选择"org.apache.maven.archetypes:maven-archteype-webapp"(在窗口直接键盘输入可搜索)<br/>
![](http://cloudnotes.nos-eastchina1.126.net/20181102123524-609239.jpg)
3. 默认下一步即可完成。

### Idea 配置Tomcat
IntelliJ IDEA社区版没有自带tomcat，可以装一个插件“smart tomcat”。
1. 配置本地Tomcat。<br/>
![](http://cloudnotes.nos-eastchina1.126.net/20181102124050-98044.jpg)
2. 安装插件：IntelliJ IDEA-Preferences-Plugins- 搜索tomcat- 选择smart tomcat-Install -重启IDEA。<br/>
![](http://cloudnotes.nos-eastchina1.126.net/20181102123930-773047.jpg)
3. 配置运行：
![](http://cloudnotes.nos-eastchina1.126.net/20181102124215-754703.jpg)
4. Run >> Edit Configurations >> 填写如下几项：
```
Name：配置的名字，随便起。一般就用项目名
Tomcat Server：tomcat的路径
Deployment：webapp所在的路径
Contex Path：上下文路径。会自己识别出来，一般我们不改这个。
Server Port：默认是8080，可以改成其它
VM options: 可选的。没有参数就不填
```
![](http://cloudnotes.nos-eastchina1.126.net/20181102124333-173431.jpg)
5. 配置好了之后，点击启动按钮，tomcat服务就启动起来了。
6. 浏览器地址栏中输入http://localhost:8080后回车，如果看到Tomcat自带的一个“猫”界面，说明你的JDK和Tomcat已搭建成功。

### IntelliJ idea创建Maven java web项目 
- 详细步骤：https://blog.csdn.net/liuhailiuhai12/article/details/53811153