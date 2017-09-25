#JavaScript 数据类型、定义对象方式
##JavaScript中数据类型：
* JavaScript中有五种原始数据类型：Undefined、Null、Boolean、Number、String。
    1. Undefined数据类型只有一个值：undefined。
    2. Null数据类型的值只有一个：null。
    3. Boolean数据类型的值有两个：true，false。
* typeof 是一元运算符，后跟变了的名称，用于获取变量的数据类型，其返回值有5个:undefined、boolean、number、string、object。
* 在JavaScript中，如果函数没有声明返回值，那么会返回 undefined。
* null与undefined的关系：undefined实际上是从null派生出来的。alert(undefined == null);
* 强制类型转换：在JavaScript中有三种强制类型转换：Boolean(value),Number(value),String(value)。
##JavaScript如何定义对象
* 在JavaScript中所有对象都是从Object对象继承过来的。Object中的属性是不可枚举的（propertyIsEnumerable 返回false），因此无法通过for..in语句得到其中的属性。
```javascrit
var object = new Object();
for(var v in Window){alert(v)};
alert(object.propertyIsEnumerable("prototype")); 
```
* **在JavaScript中可以动态添加对象的属性，也可以动态删除对象的属性**。
```javascript
var object = new Object();
//alert(object.username);
object.username = "Bob";
alert(object.username);
delete object.username; //username属性已经从object对象中删除
alert(object.username);
```
* **JavaScript中定义对象常见方式之一：直接用花括号定义对象**
```javascript
var object = {username:"bob", password:123456};
alert(object.username);
alert(object.password);
```
* **JavaScript中定义对象的几种方式（JavaScript中没有类的概念，只有对象）**：
    1. 基于已有对象扩充其他属性和方法：
    ```javascript
    var object = new Object();
    object.name = "zhangsan";
    object.sayName = function(name)
    {
        this.name = name;
        alert(this.name);
    }
    object.sayName("lisi");
    ```
    2. 工厂方式
     **这种方式的好处：让一个函数对象被多个对象所共享，而不是每一个对象拥有一个函数对象**。
    ```javascript
    function get()
        {
            alert(this.username + ", " + this.password);
        }
    function createObject(username, password)
    {
        var object = new Object();
        object.username = username;
        object.password = password;

        object.get = get
        return object;
    }
    var object1 = createObject("zhagnsan", 123);
    var object2 = createObject("lisi", "456");
    object1.get();
    object2.get();
    ```
    3. 构造函数方式
    ```javascrpt
    function Person(username, password)
    {
        this.username = username;
        this.password = password;

        this.getInfo = function()
        {
            alert(this.username + ", " + this.password);
        }
    }
    var person1 = new Person("zhangsan", 123);
    var person2 = new Person("lisi", 456);

    person1.getInfo();
    ```
    4. 使用原型（prototype）方式
    使用原型+构造函数方式来定义对象，对象之间的属性互不干扰，各个对象间共享同一个方法
    ```javascript
    // 使用原型+构造函数方式定义对象
    function Person()
    {
        this.username = new Array();
        this.password = 123;
    }
      Person.prototype.getInfo = function()
    {
        alert(this.username + ", " + this.password);
    }
      var p1 = new Person();
      var p2 = new Person();
      p1.username.push("Bob");
      p2.username.push("Jerry");
      p1.getInfo();
      p2.getInfo();
    ```
    5. 动态原型方式：在构造函数中通过标志量让所有对象共享一个方法，而每个对象拥有自己的属性。
```javascript
    function Person(username, password)
    {
        this.username = username;
        this.password = password;

        if(typeof Person.flag == "undefined")
        {
            alert("invoded");

            Person.prototype.getInfo = function()
            {
                alert(this.username + "," + this.password);
            }

            Person.flag = true;
        }
    }

    var p1 = new Person("zhangsan", 123);
    var p2 = new Person("lisi", 222);

    p1.getInfo();
    p2.getInfo();
```

