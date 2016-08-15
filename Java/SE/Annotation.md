### Annotation 工作方式
    功能： 允许开发者定义、使用自己的annotation类型。此功能由一个定义annotation类型。此功能由一个定义annotation类型的语法和一个描述annotation 声明的语法，读取annotation的API，一个使用annotation的API，一个使用annotation修饰的class文件，一个annotation处理工具（apt）组成。
### 使用系统自带注解
    Override 注解： 表示子类需要重写（override）父类对应方法 。
    Deprecated注解： 表示方法是不建议被使用的。
    SuppressWarnings注解: 表示抑制警告。
### 如何自定义注解
* **自定义注解**：当注解中的属性名为 value 时，在对其赋值时可以不指定属性的名称而直接写上属性即可；除了 value 以外的其他值都需要使用 name=value 这种赋值方式，即明确指定给谁赋值。
* 当我们使用@interface关键字定义一个注解时，该注解隐含地继承了java.lang.annotation.Annotation接口；如果我们定义了一个接口，并且让该接口继承自 Annotation，那么我们所定义的接口依然还是接口而不是注解；Annotation本身是接口而不是注解。可以与 Enum 类比。

```java
package com.bob.annotation;
// 定义一个注解
public @interface AnnotationTest {

	String value1() default "hello";	//声明一个value1成员变量默认值hello
	EnumTest value2();  // 声明一个变量
}

enum EnumTest{
	Hello, World, Welcome;
}

package com.bob.annotation;

@AnnotationTest(value2 = EnumTest.Welcome)
public class AnnotationUsage {
	
	@AnnotationTest(value1 = "world", value2 = EnumTest.Hello)
	public void method(){
		System.out.println("usage of annocation");
	}
	
	public static void main(String[] args) {

		AnnotationUsage usage = new AnnotationUsage();
		
		usage.method();
	}
}
```
## JDK提供的重要注解
1. @Retention注解，用于告诉**编译器**如何处理用户自定义的注解。
* 默认编译器会将Annotation 信息留在.class文件中，但是不会被虚拟机读取，而仅用于编译程序或者工具程序运行时提供信息。
* 在使用Retention类型时，需要提供java.lang.annotation.RetentionPoliy的枚举形态。
```java
package java.lang.annotation;
public enum RetentionPoliy
{
    SOURCE,  // 编译器处理完Annotation信息后就完成任务，不会存储在.class文件中
    CLASS,   // 编译器将Annotation存储在.class文件中,默认。运行时不会VM读取
    RUNTIME, // 编译器将Annotation存储在.class文件中。运行时可以由VM读入，所以可以通过反射读取注解
}
```
* @Retention例子
```java
package com.bob.annotation;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

// 要想通过反射从.class文件中读取注解信息，必须设置保持策略为RUNTIME
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnnotation
{
	String hello() default "hello";	// 定义一个属性hello并设置默认值
	String world();
}
```
```java
package com.bob.annotation;

@MyAnnotation(hello = "tianjin", world = "shanghai")
public class MyAnnotationTest
{
	@MyAnnotation(hello = "hangzhou", world = "ningbo")
	public void output()
	{
		System.out.println("do something!");
	}
}
```
```java
package com.bob.annotation;

import java.lang.annotation.RetentionPolicy;
import java.lang.reflect.Method;

// 通过反射获取MyAnnotationTest 类上的注解信息
public class MyReflection
{
	public static void main(String[] args) throws Exception
	{
		MyAnnotationTest myTest = new MyAnnotationTest();
		
		Class<MyAnnotationTest> clazz = MyAnnotationTest.class;	// 获取需要反射类的Class对象
		
		Method method = clazz.getMethod("output", new Class[]{});	// 获取需要操作的方法
		
		if(method.isAnnotationPresent(MyAnnotation.class))	// 判断方法上面是否注解元素
		{
			method.invoke(myTest, new Object[]{});	// 在myTest对象上执行获取的方法
		}
		
		System.out.println("-----通过修改RetentionPolicy.RUNTIME值可以发现只有Runtime的注解才可以被反射拿到----");
		
		// 获取某一个方法上特定的注解
		MyAnnotation myAnnotation = method.getAnnotation(MyAnnotation.class);
		String hello = myAnnotation.hello();
		String world = myAnnotation.world();
		System.out.println(hello + ", " + world);
	}
}
```

2. 限制注解使用时机。@Target注解限制注解可以使用的范围。在定义时要指定java.lang.annotation.ElmentType的枚举值之一。
```java
package java.lang.annotation;
public enum ElementType
{
    TYPE,  // 适用class, interface, enum
    FIELD, // 适用field
    METHOD, // 适用method
    PARAMETER, // 适用method上之parameter
    CONSTRUCTOR, // 适用constructor
    LOCAL_VARIABLE, // 适用局部变量
    ANNOTATION_TYPE, // 适用annotation型态
    PACKAGE //使用package
}
```
* 例子
```java
package com.bob.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)	// 限制MyTarget注解只能用于修饰方法
public @interface MyTarget	// 定义一个注解
{
	String value();
}
```
```java
package com.bob.annotation;

//@MyTarget("welcome")	// Error
public class MyTargetTest
{
	@MyTarget("welcome")
	public void doSomething()
	{
		System.out.println("hello world");
	}
}
```


