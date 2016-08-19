#JavaScript继承
* 对象冒充
```javascript
  function Parent(username)
  {
      this.username = username;
      this.sayHello = function()
      {
          alert(this.username);
      };
  }

  function Child(username, password)
  {
      this.method = Parent;
      this.method(username);
      delete this.method;

      this.password = password;

      this.sayWorld = function()
      {
          alert(this.password);
      };
  }

  var parent = new Parent("zhangsan");
  parent.sayHello();

  var child = new Child("lisi", "123");
  child.sayHello();
  child.sayWorld();
```
* call方法方式
call方法是Fuction对象中的方法，因此我们定义的每个函数都拥有该方法。可以用过函数名来调用call方法，call方法的第一个参数会被传递给函数中的this，从第2个参数开始，逐一赋值给函数中的参数。
```javascript
// 继承的第二种实现方式，call方法方式，Function对象中的方法
    function test(str, str2)
    {
        alert(this.name + ", " + str + ", " + str2);
    }

    var object = new Object();
    object.name = "aaa";

    //test.call相当于调用了test函数
    test.call(object, "bbb", "ccc");    //将object赋值给了this
```
例子：
```javascript
function Parent(username)
    {
         this.username = username;
         this.sayHello = function()
         {
             alert(this.username);
         };
    }

    function Child(username, password)
    {
        Parent.call(this, username);
        // 通过apply方法方式
        //Parent.apply(this, new Array(username));

        this.password = password;

        this.sayWorld = function()
        {
            alert(this.password);
        };
    }
    var parent = new Parent("zhangsan");
    parent.sayHello();

    var child = new Child("lisi", "123");
    child.sayHello();
    child.sayWorld();
```
* 使用混合方式
```javascript
// 使用混合方式方式实现继承（推荐）
    function Parent(hello)
    {
        this.hello = hello;
    }
    Parent.prototype.sayHello = function()
    {
        alert(this.hello);
    };

    function Child(hello, world)
    {
        Parent.call(this, hello);

        this.world = world;
    }
    Child.prototype = new Parent();
    Child.prototype.sayWorld = function()
    {
        alert(this.world);
    }

    var child = new Child("Bob", 111);
    child.sayHello();
    child.sayWorld();
```

