# Java IO
# Java流
### Java流的结构可以分为*字节流*和*字符流*
    字节流（Byte Streams）： InputStream、OutputStream，字节流的输入输出操作由着两个类的子类实现。
    字符流（Character Streams）： Reader、Writer。
    底层，所有的输入/输出都是字节形式的。

#### File 类的基本使用
```java
package com.study.io;

import java.io.File;

/**
 * 删除指定目录
 *
 */
public class FileTest2
{
	public static void deleteAll(File file)
	{
		if(file.isFile() || file.list().length == 0)	// list方法获取文件夹目录的名字数组
		{
			file.delete();
		}
		else
		{
			File[] files = file.listFiles();
			
//			System.out.println(file.list()[0]);
			
			for(File f : files)
			{
				deleteAll(f);
				
				f.delete();
			}
			file.delete();
		}
		
	}
	
	public static void main(String[] args)
	{
		FileTest2 test = new FileTest2();
		test.deleteAll(new File("C:\\abc"));
	}
}
```
#### 输入字节流 FileInputStream 
```java
package com.study.io;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

/**
 * 使用字节流读取文件
 *
 */
public class InputStreamTest1
{
	public static void main(String[] args) throws IOException
	{
		InputStream is = new FileInputStream(new File("C:" + File.separator + "HaxLogs.txt"));
		
		byte[] buffer = new byte[200];
		
		int length = 0;		// 每次实际读的长度
		
		while(-1 != (length = is.read(buffer, 0, 200)))	
		{
			System.out.println(length);
			
			String str = new String(buffer, 0, length);
			
			System.out.println(str);
			
		}
		
	}
}
```
 #### 输出字节流 FileOutputStream
 ```java
 package com.study.io;

import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;

/**
 * 使用字节流讲内容写入到文件
 *
 */
public class OutputStreamTest1
{
	public static void main(String[] args) throws IOException
	{
//		OutputStream output = new FileOutputStream(new File("C:" + File.separator + "output.txt"), true);
		OutputStream output = new FileOutputStream(new File("output.txt"), true);
		
		String str = "hello world! ";
		
		output.write(str.getBytes(), 0, str.length());
		
		output.close();
		
	}
}
 ```
#### 输入/输出的字符流
```java
package com.study.io;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;

public class InputStreamReaderTest
{
	public static void main(String[] args) throws IOException
	{
		FileOutputStream fos = new FileOutputStream("file.txt");
		
		OutputStreamWriter osw = new OutputStreamWriter(fos);
		
		BufferedWriter bw = new BufferedWriter(osw);
		
		bw.write("www.baidu.com");
		bw.write("\n");
		bw.write("www.google.com");
		
		bw.close();
		
		FileInputStream fis = new FileInputStream("file.txt");
		
		InputStreamReader isr = new InputStreamReader(fis);
		
		BufferedReader br = new BufferedReader(isr);
		
		String str = br.readLine();
		
		while(null != str)
		{
			System.out.println(str);
			str = br.readLine();
		}
		
		br.close();
		
	}
}
```
### Java IO的原理：装饰模式（Decorator）
* 装饰模式又名包装（Wrapper）模式，以对客户端透明的方式**动态**扩展**对象**的功能，是继承关系的一个替代方案。可以在不创造更多子类的情况下，将对象的功能加以扩展。
* 装饰模式把客户端的调用委派到被装饰类。
* 装饰模式的角色：
    1. 抽象构件角色（Component） ：给出一个抽象接口，以规范准备接收附加责任的对象。
    2. 具体构件角色（Concrete Component） ：定义一个将要接收附加责任的类。
    3. 装饰角色（Decorator） ： 持有一个构件（Component）对象的引用，并定义一个与抽象构件接口一致的接口
    4. 具体装饰角色（Concrete Decorator） ：负责给构件对象“贴上”附加的责任
代码实现：
```java
package com.study.decorator;

/**
 * 抽象构建角色：给出一个抽象接口，以规范准备接收附加责任的对象。
 * 比如java中的：OutputStream
 *
 */

public interface Component
{
	public void doSomething();
}
```
```java
package com.study.decorator;

/**
 * 具体构建角色：定义一个将要接收附加责任的类。
 * 比如Java中的：FileOutputStream
 *
 */
public class ConcreteComponent implements Component
{
	@Override
	public void doSomething()
	{
		System.out.println("功能A");
	}

}
```
```
package com.study.decorator;

/**
 * 
 * 装饰角色：持有一个构件（Component）对象的引用，并定义一个与抽象构件接口一致的接口。
 * 比如：javaIO中的FilterOutputStream
 * 装饰对象包含一个真实对象的引用（reference）,接收所有来自客户端的请求。它把这些请求转发给真实的对象。
 */
public class Decorator implements Component
{
	private Component component;
	
	public Decorator(Component component)
	{
		this.component = component;
	}
	
	@Override
	public void doSomething()
	{
		component.doSomething();
	}
}
```
```java
package com.study.decorator;

/**
 * 
 * 具体装饰角色：负责给构件对象“贴上”附加的责任。
 * 比如Java中的BufferedOutputStream
 *
 */
public class ConcreteDecorator1 extends Decorator
{
	public ConcreteDecorator1(Component component)
	{
		super(component);
	}
	
	@Override
	public void doSomething()
	{
		super.doSomething();
		
		this.doAnotherThing();	// 给构件对象“贴上”附加的责任
	}
	
	private void doAnotherThing()
	{
		System.out.println("功能B");
	}
	
	
}
```
```java
package com.study.decorator;

/**
 * 具体装饰角色
 *
 */
public class ConcreteDecorator2 extends Decorator
{
	public ConcreteDecorator2(Component component)
	{
		super(component);
	}
	
	@Override
	public void doSomething()
	{
		super.doSomething();
		
		this.doAnotherThing();
	}
	
	private void doAnotherThing()
	{
		System.out.println("功能C");
	}
}
```
使用
```java
package com.study.decorator;

public class Client
{
	public static void main(String[] args)
	{

		Component component = new ConcreteComponent();

		Component component2 = new ConcreteDecorator1(component);

		component2.doSomething();

		System.out.println("------------");

		Component component3 = new ConcreteDecorator2(component2);

		component3.doSomething();

//		Component component = new ConcreteDecorator1(new ConcreteDecorator2(new ConcreteComponent()));
//
//		component.doSomething();
	}
}
```

