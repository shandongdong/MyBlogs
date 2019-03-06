# Robot_Framework之jybot模式_Java开发Library
最近在windows下用Robot Framework框架来做java接口自动化，现跟大家分享下经验
## 环境搭建
### Python
- [Python](https://www.python.org/)RF框架是基于python 的，所以一定要有python环境，建议使用2.7版
- 安装路径加入系统路径path（C:\Python27;C:\Python27\Scripts;）
### Jdk1.7
- http://www.oracle.com/technetwork/java/javase/downloads/index.html
#### 环境配置
- 新建JAVA_HOME变量（本人是C:\Program Files\Java\jdk1.7.0_75） 
- 添加系统路径到path（%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;）
- 新建CLASSPATH变量（.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar）
### Jython
- http://www.jython.org/downloads.html 
- Python语言的java实现，测试java接口的关键，建议下载jar包进行安装
- 安装：java -jar jython-installer-2.7.0.jar 
- 环境配置：
	-新增JYTHON_HOME，并设置为安装路径
	-配置classpath、path路径: %JYTHON_HOME%\Lib;%JYTHON_HOME%\bin;
### Robot framework
- https://pypi.python.org/pypi/robotframework 
这个不解释，RF框架。建议下载源码编译
python setup.py install（pybot模式下必须）
jython setup.py install（jybot模式下必须）

### wxPython
http://www.wxpython.org/download.php
下载 wxPython2.8-win64-unicode-2.8.12.1-py27.exe 版本
wxPython的文件，确认名字是wxPython2.8-win64-unicode-2.8.12.1-py27.exe，不要有差错。win64是因为Python是64位的，unicode是必须的，2.8.12.1是RIDE稳定支持的版本，py27是Python2.7系列的，因为Python是64位的，所以其他配套的程序都要以Python为准，也要64位的。
一个GUI库，因为RIDE 是基于Wxpython开发的，所以这个必须安装建议使用编译好的exe文件进行安装，默认安装在python目录下
 
### robotframework-ride
	https://pypi.python.org/pypi/robotframework-ride
RIDE就是一个图形界面的用于创建、组织、运行测试的软件
安装： jython setup.py install 
	    python steup.py install
修复一个问题
安装好以上软件后，发现ride跑jybot模式有报错，页面不标注结果并且无message log
解决方法：修改一个文件
C:\Python27\Lib\site-packages\robotide\contrib\testrunner\TestRunnerAgent.py
源码
    def _send_pid(self):
        self._send_socket("pid", os.getpid())
```python
    def _send_pid(self):
        os_methods = dir(os)
        if 'getpid' in os_methods:
            self._send_socket("pid", os.getpid())
        else:
            self._send_socket("pid", "0")

```
### Eclipse
Java开发工具
## 运行Ride
> cmd ride.py

## java自定义库
- 重要：首先创建一个目录（如D:\JavaLibs），并把目录加入CLASSPATH，添加的路径为(D:\JavaLibs\*;)，注意后面的*
### 准备java关键字库
- 创建一个java关键字库文件JavaTestLib.java，并打成jar包放入D:\JavaLibs\下
```java
public class JavaTestLib {
	public void javaKeyword1() {
		System.out.println("I am javaKeyword1!");
	}
	public void javaKeyword2() {
		System.out.println("I am javaKeyword2!");
	}
}
```
![](http://cloudnotes.nos-eastchina1.126.net/20190306101401-612368.jpg)
多个java库文件可打入同一个jar包里，jar包名可随意
打包：项目右键ExportJava—JAR fileNextFinish。然后将生成的jar包拷贝到D:\JavaLibs 目录下即可
### 准备库的文档xml
Cmd命令输入：> jython -m robot.libdoc com.bob.junit.JavaTestLib com.bob.junit.JavaTestLib.xml \
一个.java库文件对应一个xml
会在当前目录下生成一个com.bob.junit.JavaTestLib.xml文件，把生成好的xml文件放入python根目录下即可（如C:\Python27）
![](http://cloudnotes.nos-eastchina1.126.net/20190306101435-731718.jpg)
### 引入库并使用关键字
#### 正常情况：(上面的xml文件生成成功)
打开ride.py新建项目，导入自定义库文件。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101456-966480.jpg)
填写关键字（方法全名）
![](http://cloudnotes.nos-eastchina1.126.net/20190306101505-330826.jpg)
库名在xml里有name=" com.bob.junit.JavaTestLib "
执行结果
![](http://cloudnotes.nos-eastchina1.126.net/20190306101511-701696.jpg)

#### 异常情况：（上面的xml文件生成失败）
注意：导入的Libray要为某个类的全路径。例如：包名.类名(com.bob.junit.JavaTestLib)
![](http://cloudnotes.nos-eastchina1.126.net/20190306101527-544892.jpg)
这里之所以为红色是因为我上面的步骤生成 xml 文件失败造成的。不生成xml文件也可以使用，但是使用关键字的时候颜色为黑色，没有提示。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101535-748919.jpg)
#### 生成带说明的关键字
- 通过生成的“com.bob.junit.JavaTestLib.xml”文件
```xml
<kw name="Java Keyword 1">
<arguments>
</arguments>
<doc>手动添加注释</doc>
<tags>
</tags>
</kw>
```
- 可以发现<doc></doc>里面为空，虽然在java文件中使用了/**为方法添加了注释但是，注释的内容没有生成到doc里。手动在doc标签写上注释可以发现在Ride中即可查看注释。关于java代码中的方法注释如何生成到xml文件还未调通。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101621-473690.jpg)

## RF脚本编写、库导入，使用笔记
### 在RF中使用Java关键字的步骤为
1. 将整个项目导出为“Runnable JAR file”（如果只是打包成jar包，那么项目中相关依赖的jar文件不会打包到生成的jar中）；
![](http://cloudnotes.nos-eastchina1.126.net/20190306101644-579776.jpg)
2. 在RF的TestSuite 的Library中导入具体类名全称（com.dh.robot.test.testcase.BasicTestCase）。RF使用此类无需先在BasicTestCase.java中new此类，jython底层会自动创建这个类的实例，值需要保证此类中的方法都为可运行的方法即可。如果此类中有调用其他类按照Java正常编译要求编写即可。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101708-299836.jpg)
3. 在RF中新建TestCase直接使用导入类的方法即可。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101721-438551.jpg)
总结：1. 在RF中要想使用Java关键字，将程序打包成可运行的Jar，然后在RF中导入具体的类名即可使用。2. 从上面的三部可以得出结论如果想让RF识别出是否存在此关键字则需要使用 jython -m robot.libdoc工具生成xml文件并放入Python根目录即可。
![](http://cloudnotes.nos-eastchina1.126.net/20190306101741-233829.jpg)
### RF中重名关键字如何调用？
在Java中编写的方法，不同类文件方法名称可以相同。Java在调用时可以根据引用名称.出具体方法，但是RF如果需要使用具体某个类中的明确方法，需要使用Library.keyword方式(com.dh.robot.test.testcase.temp.RFMain.init);
![](http://cloudnotes.nos-eastchina1.126.net/20190306101755-289603.jpg)
### Java生成XML文件注意事项
1. 最好为每个类提供一个不带参数的构造方法。
前面写到，我们无需再代码里主动new这个类，生成关键字时直接调用方法即可。是因为jython底层已经帮我们自动化的创建了这个类。如果一个类没有提供不带参数的构造方法(或者构造方法私有化)，那么执行jython -m robot.libdoc生成xml文件时会生成失败，提示“Expected at least 2 arguments, got 1.”。
		构造方法私有化会报：with no arguments failed: TypeError: No visible constructors for class（***）
2. 枚举、接口、抽象类无需生成xml文件。
   报错：with no arguments failed: TypeError: No visible constructors for class（***），因为就算提供了构造方法，这些类型本身就不能产生实例。
## Jython库
一开始因为不会java，尝试了下写jython封装自定义库，后面考虑维护问题，才改成java封装，jython语言资料网上有，不多讲。
数据库例子
有人已经写了，直接上网址吧
http://blog.sina.com.cn/s/blog_654c6ec70100uifs.html


# 官方资料
https://github.com/ThomasJaspers/robotframework-dblibrary/wiki

