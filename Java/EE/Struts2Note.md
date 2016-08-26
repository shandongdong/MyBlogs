# Struts
* struts 的类型转换，对于8个原声数据类型以及Date、String等常见类型，Struts2可以使用内建的类型转换器实现自动转换；但对于自定义的对象类型来说，就需要我们自己指定类型转换的方式了。
* struts类型转换器流程：对于自定义的类型转换器来说需要提供3个信息：Action的名字、Action 中待转换的属性名以及该属性对应的类型转换器。其中Action的名字是通过属性文件名来获得的、Action中的待转换的属性名是通过属性文件中的key来获得的，该属性对应的类型转换器是通过该key所对应的value来获得的。
* 例子：
1. 创建input.jsp
```html
  <body>
    <h1>使用分号隔开用户名和密码</h1><br/>
    <form action="userAction.action">
    	user info:<input type="text" name="user" /><br />
    	
    	<input type="submit" value="submit" />  
    </form>
    
  </body>
```
2. 配置当前包下面的struts.xml
```xml
		<action name="userAction" class="com.study.struts2.UserAction">
			<result name="success">/output.jsp</result>
		</action>
```
3. 创建bean
```java
package com.study.bean;

public class User
{
	private String username;
	private String password;
	public String getUsername()
	{
		return username;
	}
	public void setUsername(String username)
	{
		this.username = username;
	}
	public String getPassword()
	{
		return password;
	}
	public void setPassword(String password)
	{
		this.password = password;
	}
	
}
```
4. 创建UserAction类
```java
package com.study.struts2;

import com.opensymphony.xwork2.ActionSupport;
import com.study.bean.User;

public class UserAction extends ActionSupport
{
	private User user;

	public User getUser()
	{
		return user;
	}

	public void setUser(User user)
	{
		this.user = user;
	}
	
	@Override
	public String execute() throws Exception
	{
		System.out.println("username " + user.getUsername());
		System.out.println("password " + user.getPassword());
		
		return SUCCESS;
	}
}
```
5. 创建转换器
```java
package com.study.converter;

import java.util.Map;
import java.util.StringTokenizer;

import ognl.DefaultTypeConverter;

import com.study.bean.User;

public class UserConverter extends DefaultTypeConverter
{
	@Override
	public Object convertValue(Map context, Object value, Class toType) 
	{
		if(User.class == toType)	//从页面向后台对象转换
		{
			String[] str = (String[])value;
			
			String firstValue = str[0];
			StringTokenizer st = new StringTokenizer(firstValue, ":");
			
			String username = st.nextToken();
			String password = st.nextToken();
			
			User user = new User();
			
			user.setUsername(username);
			user.setPassword(password);
			
			return user;
		}
		else if(String.class == toType)	//从后台对象向页面转换
		{
			User user = (User)value;
			
			String username = user.getUsername();
			String password = user.getPassword();
			
			String userInfo = "username: " + username + ", " + "password: " + password;
			
			return userInfo;
		}
		
		return null;
	}
}
```
6. 创建.propertice文件，将action与转换器关联上
```java
user=com.study.converter.UserConverter
```
7. 创建结果输出页面,使用struts标签库
```html
<%@ taglib prefix="s" uri="/struts-tags" %>

  <body>
  
    <s:property value="user" />
    
  </body>
```
* 可以使用struts2框架自带的类型转换器StrutsTypeConverter 简化类型转换代码的编写，StrutsTypeConverter继承与DefaultTypeConverter父类，并且提供了2个抽象方法：convertFromString 与convertToString，分别表示从页面的字符串转换为后台对象以及从后台对象转换为页面的字符串，我们只需实现这两个抽象方法即可实现类型转换。
```java
package com.study.converter;

import java.util.Map;
import java.util.StringTokenizer;

import org.apache.struts2.util.StrutsTypeConverter;

import com.study.bean.User;

public class UserConverter2 extends StrutsTypeConverter
{

	@Override
	public Object convertFromString(Map context, String[] values, Class toClass)
	{
		//copy from UserConverter
		String firstValue = values[0];
		StringTokenizer st = new StringTokenizer(firstValue, ":");
		
		String username = st.nextToken();
		String password = st.nextToken();
		
		User user = new User();
		
		user.setUsername(username);
		user.setPassword(password);
		
		return user;
	}

	@Override
	public String convertToString(Map context, Object o)
	{
		//copy from UserConverter
		User user = (User)o;
		
		String username = user.getUsername();
		String password = user.getPassword();
		
		String userInfo = "username: " + username + ", " + "password: " + password;
		
		return userInfo;
	}

}
```
* 全局类型转换。在 src 目录下新建 xwork-conversion.properties，该文件的内容是待转换类=转换器名字，即com.study.bean.User=com.study.converter.UserConverter
* 自定义方法：Struts也支持自定义方法，即在struts.xml中的action元素内定义method属性，属性值即为待执行的方法。其中，该方法的声明要与 execute 保持一致。**但是不推荐这种方式，因为它容易导致 Actino 代码混乱**。
* 执行流程
1. 首先进行类型转换
2. 然后进行输入校验（执行 validate 方法）
3. 如果在上述过程中出现任何错误，都不会在去执行 execute 方法，会转向 struts.xml 中该action 的名为 input 的 result 所对应的页面。
* ActionSupport 类的 addActionError() 方法的实现:首先创建一个ArrayList对象，然后将错误信息添加到该ArrayList对象中。
* 当调用 getActionErrors()方法返回Action级别的错误信息列表时，返回的实际是集合的一个副本而不是集合本身，因此对集合副本调用clear()方法清除的依旧是副本中的元素而非原集合中的元素，此时原集合中的内容没有收到任何影响。**换句话说，Action级别的错误信息列表对开发者来说是只读的**。
* FiledError级别的错误信息底层是用LinkedHashMap实现的，该Map的key是String类型，value是List<String>类型，这就表示一个 Field Name 可以对应多条错误信息，这些错误信息都放置在List<Sting>集合当中。
* 自定义方法校验
```xml
		<action name="login" class="com.study.struts2.LoginAction" method="myExecute"> 
			<result name="success">/result.jsp</result>
		</action>
```
```java
package com.study.struts2;

import java.util.Map;


import com.opensymphony.xwork2.ActionSupport;

public class LoginAction extends ActionSupport
{
	private String username;
	private String password;
	public String getUsername()
	{
		return username;
	}
	public void setUsername(String username)
	{
		this.username = username;
	}
	public String getPassword()
	{
		return password;
	}
	public void setPassword(String password)
	{
		this.password = password;
	}
	
	public String execute()
	{
		return "success";
	}
	
	//自定义方法，在struts.xml文件中声明了执行时机
	public String myExecute()
	{
		System.out.println("myExecute invoked!");
		
		return SUCCESS;
	}
	//自定义验证方法，用于验证myExecute方法，注意方法命名规则
	public void validateMyExecute()
	{
		System.out.println("validateMyExecute invoked！！");
		
		this.addActionError("action error");
		
		this.clearActionErrors();
		this.clearFieldErrors();
	}
	
	@Override
	public void validate()
	{
		System.out.println("validate invoked!");
		this.addActionError("action error");
	}

}
```
Action中自定义方法的输入校验。对于通过action的method属性所指定的自定义方法，其对应的自定义输入校验方法名为validateMyExecute(假设自定义的方法名为myExecute)。
* 当在 Action 中指定了自定义的 execute 方法时，首先会执行自定义的 execute 方法所对应的输入校验方法，然后在去执行 validate 方法，执行完毕后如果出现任何错误都不会在去执行自定义的 execute 方法，流程转向了 input 这个名字所对应的页面上。
* 自定义 Field级别的错误提示消息：
1. 新建一个以 Action 名命名的 .propertice文件，如RegisterAction.propertice。
2. 然后在该属性文件中指定每一个出错字段的错误信息。**invalid.fieldvalue**.birthday=birthday invalid!!!
* Struts2的校验框架（有效的 xml 文件）。具体来说分为字段优先校验器与校验器优先校验器。
* Struts2框架校验执行的先后顺序：
1. 首先执行校验框架（xml文件）。
2. 执行自定义方法的校验方法（validateMyExecute）。
3. 执行validate 方法。
* 对于 struts.xml文件的配置来说，局部要优于全局。我们既可以 Action 中定义异常与结果，也可以定义全局的异常与结果，**局部总是优于全局**，如果定义成全局，那么可以为所有的Actin所公用，而局部的异常与结果只能被当前 Action 所独享，不能为其他Action所共享。
* Preparable 接口的作用是让 Action 完成一些初始化工作，这些初始化工作是放在Preparable接口的prepare方法中完成的，该方法会在 execute 方法执行之前得到调用。
* 防止表单重复提交的两种方式：
1. 通过重定向。
2. **通过 Session Token（Session令牌）**:当客户端请求页面时，服务器会通过 token 标签生成一个随机数，并且将该随机数放置到session当中，然后将该随机数发向客户端；如果客户第一次提交，那么浏览器会将该随机数发往服务器，服务器会接收到该随机数并且与 session 中所保存的随机数进行比较，这时两者的值相同的，服务器认为是第一次提交，并且将更新服务器端的这个随机数；如果此时再次重复提交，那么客户端发送服务器的随机数还是之前那个，而服务器端的随机数则已经发生了变化，两者不同，服务器就认为这是重复提交，进而转向 invalid.token所指向的结果页面。
范例；
新建token.jsp页面,使用struts提供的token标签
```html
<%@ taglib prefix="s" uri="/struts-tags" %>

  <body>
  	<s:form action="token.action" theme="simple">
  	username: <s:textfield name="username"></s:textfield><br/>
  	password: <s:password name="password"></s:password><br />
  	
  	<s:token></s:token>
  	
  	<s:submit value="submit"></s:submit>
  	
  	</s:form>
  </body>
```
新建tokenSuccess.jsp页面
```html
<%@ taglib prefix="s" uri="/struts-tags" %>
  <body>
    username: <s:property value="username"/>
    password: <s:property value="password"/>
  </body>
```
新建tokenFail.jsp
```html
  <body>
     	不要重复提交表单
  </body>
```
配置struts.xml文件
```xml
    <package name="struts2" extends="struts-default">
		<action name="token" class="com.study.struts2.TokenAction">
			<result name="success">/tokenSuccess.jsp</result>
			<result name="invalid.token">/tokenFail.jsp</result>
			
			<interceptor-ref name="token"></interceptor-ref>
			<interceptor-ref name="defaultStack"></interceptor-ref>
			
		</action>
    </package>
```
通过打开token.jsp页面可以发现<s:token>标签会生成2个隐藏域。
![Token简单原理图](https://github.com/shandongdong/MyBlogs/blob/master/Java/Pictrue/sessionToken%E6%9C%BA%E5%88%B6%E5%9B%BE.png?raw=true)
![Token生成源码解析](https://github.com/shandongdong/MyBlogs/blob/master/Java/Pictrue/sessionToken%E6%9C%BA%E5%88%B6%E6%BA%90%E7%A0%81.png?raw=true)

