## 环境搭建
> https://blog.csdn.net/lom9357bye/article/details/72796668
> https://blog.csdn.net/lom9357bye/article/category/6944174
## 第一个Spring程序
1. 创建Maven工程，修改pom.xml
```xml
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.3.22.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.22.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.4</version>
        </dependency>
    </dependencies>
```
2. 创建People.java
```java
public class People {
    private String userName;
    public String getUserName() {
        return userName;
    }
    public void setUserName(String userName) {
        this.userName = userName;
    }
    @Override
    public String toString() {
        return "People{" +
                "userName='" + userName + '\'' +
                '}';
    }
    public void sayHello() {
        System.out.println("Hello World Spring!" + userName);
    }
}
```
3. 在resources目录下创建applicationContext.xml（名称随意）
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="people" class="com.github.study.spring.People">
        <property name="userName" value="Welcome"></property>
    </bean>
</beans>
```
4. 测试环境配置。创建Test1.java
```java
public class Test1 {
    public static void main(String[] args) {
        // 1. 创建Spring的IOC容器对象
        ApplicationContext context = new ClassPathXmlApplicationContext("spring/applicationContext.xml");
        // 2. 获取People对象
        People people = (People) context.getBean("people");
        people.sayHello();
    }
}
```
