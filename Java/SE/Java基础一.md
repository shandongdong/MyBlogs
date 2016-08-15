#Java 基础一

* 在Java中int永远是32为的整数
* 什么是Applet：在网页中运行Java程序称为Applet。任何支持Java的web浏览器都可以运行applet。
*Java中局部变量必须赋值才能使用，全局变量可以不用赋值，全局变量拥有默认值
```java
public class FirstSample
{
	private static int globalVariable;	//全局变量拥有默认值

	public static void main(String[] args)
	{
		System.out.println("We will note use 'Hello,World!'");

		int localVariable;	//局部变量必须赋值
		System.out.println(globalVariable);
		System.out.println(localVariable);

	}
}
``` 
* 块作用域
    块（即复合语句）是指由一对花括号括起来的若干条简单的Java语句。块确定了变量的作用域。
* 调用构造器JVM处理步骤：
    1. 所有数据被初始化为默认值（0、false或null）。
    2. 按照在类声明中出现的次序，依次执行所有域初始化语句和初始化块。
    3. 如果构造器第一行调用了第二个构造器，则执行第二个构造器主体。
    4. 执行这个构造器主体。

