PHP中有一类特别的系统方法，它们统一以__开头，使用语义清晰简单，这类形式特殊、作用特殊的方法被称为魔术方法。

常见的魔术方法有：

```php
__construct()、__destruct()
__call()、__callStatic()
__get()、__set()
__isset()、__unset()
__sleep()、__wakeup()
__toString()、__invoke()、__clone()、__set_state()、__debugInfo()
```

这些魔术方法往往是成对出现的，下面把一对对魔术方法拿出来做对比分析。



#### `__construct()` 和 `__desctuct()`

这两个魔术方法都是在PHP5中开始出现支持的函数。

`__construct()` **为构造函数，如果一个类具有构造函数，则这个类的对象在进行实例化时都会先调用这个方法，非常适合用来做一些初始化的工作。**

如果不使用构造函数对对象的属性初始化，那么我们需要在对象产生后对对象的属性进行初始化，如：

```php
$tom = new people();
$tom->name = 'Tom';
$tom->age = 18;
$tom->sex = 'boy';
```

如果使用构造函数，创建对象并完成初始化的代码变为：

```php
<?php
 
class people
{
    public $name;
    public $age;
    public $sex;

    public function __construct($name, $age, $sex)
    {
        $this->name = $name;
        $this->age = $age;
        $this->sex = $sex;
        echo "A people was born!"."<br/>";
    }

    public function description()
    {
        echo $this->name, " is a ", $this->age, " years old", $this->sex;
    }
}

 $codeman = new people('codeman',22,'man');
```

当然，对对象数据属性的初始化只是构造函数的一个应用，很多时候在执行一个类的所有方法之前都要进行某项操作或检测，比如是否登录、是否达到权限等级等，非常适合用构造函数来实现，代码清晰整洁。



`__destruct()`**析构函数和构造函数相反，是在对象的所有引用都被删除或者当对象被显示销毁时执行，主要是用来做一些内存资源等的释放工作，具体的应用场景要看需求而言，实际应用并不是很多**。

```php
<?php
class people
{
    public $name;
    public $age;
    public $sex;

    public function __construct($name, $age, $sex)
    {
        $this->name = $name;
        $this->age = $age;
        $this->sex = $sex;
        echo "A people was born!"."<br/>";
    }

    public function description()
    {
    	echo $this->name, " is a ", $this->age, " years old", $this->sex;
    }

    public function __destruct()
    {
    	echo "A people was die!"."<br/>";
    }
}

$codeman = new people('codeman', 22, 'man');
var_dump($codeman);

// 输出结果
A people was born!
object(people)[1]
public ‘name’ => string ‘codeman’ (length=7)
public ‘age’ => int 22
public ‘sex’ => string ‘man’ (length=3)
A people was die!
```

可以看到构造函数在类被创建时调用，析构函数在脚本的末尾被调用，在脚本结束之前执行的任何语句如上面对对象的输出，都会在执行析构函数之前被执行。

**注意：**

（1）**PHP 子类的构造函数不会先去执行父类的构造函数，需要分别在构造函数和析构函数中使用`parent::__construct()` 和 `parent::__dertruct()`来显式调用父类的构造和析构函数。当然，如果一个子类没有自己的构造函数，那么假如父类有构造函数，子类创建对象时会自动去调用父类的构造函数，析构函数也是如此。**

```php
<?php
class A
{
    public function __construct()
    {
    	echo "A was create!<br/>";
    }
    
    public function __destruct()
    {
    	echo "A was destroy!<br/>";
    }
}

class B extends A
{
    public function __construct()
    {
    	echo "B was create!<br/>";
    }
    
    public function __destruct()
    {
    	echo "B was destroy!<br/>";
    }
}

class C extends A
{

}

$a = new A();
$b = new B();
$c = new C();

// 输出结果
A was create!
B was create!
A was create!
A was destroy!
B was destroy!
A was destroy!
```

（2）析构函数即使在使用 exit() 和 die() 终止脚本运行时也会被调用，这很好理解，上面我们已经说过了，析构函数在任何语句执行完之后执行。

我们在上面的代码末尾加上一行exit()，执行结果并没有发生变化

```php
 # previous code 
exit();
```

（3）在析构函数里执行exit()会中止其他关闭操作的运行。

```php
  class B extends A
  {
      public function __construct()
      {
          echo "B was create!<br/>";
      }

      public function __destruct()
      {
          exit();
          echo "B was destroy!<br/>";
      }
  }

// 执行结果如下：
A was create!
B was create!
A was create!
A was destroy!
```



#### `__call()` 和 `__callStatIc()`

当我们调用了一个不存在的方法时，会发生什么？会报错

如果我们想在代码发生调用了不存在的方法时做点什么怎么办？使用`__call()`魔术方法

同样的当调用了一个不存在的静态方法想做点什么，用`__callStatic()`魔术方法

```php
<?php
 class Test
 {
     public function __call($name, $arguments)
     {
         echo "Calling object method '$name' " . implode(', ', $arguments). "<br/>";
     }

     public static function __callStatic($name, $arguments)
     {
         echo "Calling static method '$name' " . implode(', ', $arguments). "<br/>";
     }
 }

 $obj = new Test();

 $obj->runTest('in object context');
 Test::runTest('in static context');

// 输出结果：
Calling object method ‘runTest’ in object context
Calling static method ‘runTest’ in static context
```

这两个方法是为了在调用了不存在的方法时发生点什么，如防止系统报错，自己包一层异常处理等，但这并不是魔术方法存在的真正意义，魔术方法可以让类和方法的动态创建有了可能性，这在MVC框架的设计里是很有用的。

借助这两个魔术方法，我们还可以实现类似于JS中的链式调用，在很多PHP MVC框架中大量开发封装了这种链式调用的方法。

现在来实现一个简单的类似 JS 的字符串链式调用，在PHP中要求一个字符串的真正长度会这样写：

strlen(trim($str));

我们要实现这样的优雅地调用：

$str->trim()->strlen();

实现实例代码如下：

```php
class Strings
{
    public $str = '';

    public function __construct($str)
    {
        $this->str = $str;
    }

    public function __call($name, $arguements)
    {
        $ret = '';
        switch ($name) {
            case 'trim':
                $new_s = trim($this->str);
                $ret = new Strings($new_s);
                break;
            case 'strlen':
                $ret = strlen($this->str);
                break;
            default:
        }
        return $ret;
    }
}

$s = new Strings(' codeman ');
$length = $s->trim()->strlen();
echo $length;
```

更好的写法：

```php
 public function __call($name, $arguements)
 {
     $ret = call_user_func($name, $this->str);
     switch ($name){
         case 'trim':
             $ret = new Strings($ret);
             break;
         default:
     }
     return $ret;
 }
```

**在链式调用中特别注意要明确每次调用后返回值是什么，是普通变量还是一个对象，还是新创建的对象。**



#### `__get()` 和 `__set()`

前面说到调用一个不存在的方法会触发 `__call()`，那使用到了不存在的类的属性值呢？会触发 `__get()` 和 `__set()`

当调用一个类不存在的属性时，会触发 `__get()`；

当给一个类不存在的属性赋值时，会触发 `__set()`。

注意，当引用一个动态创建的属性时也会调用 __get() 函数。

使用这个两个魔术方法，可以在PHP中实现动态地操作变量，这就是PHP中的重载。

注意：PHP中的重载与其它绝大多数面向对象语言不同。传统的”重载”是用于提供多个同名的类方法，但各方法的参数类型和个数不同。

使用实例代码：

```php
<?php

header('Content-type:text/html; charset=utf-8');

class Account
{
    private $user = 1;
    private $pwd = 2;

    public function __set($name, $value)
    {
        echo "Setting $name to $value \r\n";;
        $this->$name = $value;
    }

    public function __get($name)
    {
        if (!isset($this->$name)) {
            echo '未设置<br/>';
            $this->$name = "正在为你设置默认值";
        }
        return $this->$name;
    }
}

$a = new Account();
echo $a->user;  // 这里会调用__get方法
echo "<br/>";
$a->name = 5;   // 这里会调用__set方法
echo "<br/>";
echo $a->name;
```



#### `__isset()` 和 `__unset()`

我们经常使用 `isset()` 和 `unset()` 函数分别来判断一个变量是否被设置了值和释放变量。

假如判断或释放了不存在、动态创建的变量，会调用 `__isset()` 和 `__unset()`。

在手册里是这么说的：

**当对不可访问属性调用 isset() 或 empty() 时**，`__isset()` 会被调用。
**当对不可访问属性调用 unset() 时**，`__unset()` 会被调用。

**注意：属性重载只能在对象中进行。在静态类中，这些魔术方法将不会被调用。所以这些方法都不能被声明为 static。**

实例代码如下：

```php
<?php
class Test
{
    private $val = '1';

    public function __get($name)
    {
        echo "Getting '$name'\n";
        if (array_key_exists($name, $this->data)) {
            return $this->data[$name];
        } else {
            return null;
        }
    }

    public function __isset($name)
    {
        echo "__isset() is used!\n";
        if (isset($this->$name)) {
            echo $this->$name . "\n";
        } else {
            echo $name . " is not set!\n";
        }
    }

    public function __unset($name)
    {
        echo "__unset() is used!\n";
        unset($this->$name);
    }
}

echo "<pre>";
$obj = new Test();
isset($obj->val);
unset($obj->val);
isset($obj->val);

// 输出结果：
__isset() is used!
1
__unset() is used!
__isset() is used!
val is not set!
```



#### `__sleep()` 和 `__wakeup()`

我们经常使用 serialize() 和 unserialize() 函数来序列化和反序列化对象。

对象的序列化实际上是将对象的属性按照属性数组+指针的形式进行序列化存储。

有的时候我们对象的属性非常多，但是我们只是想存储部分有用的属性，如何实现？

答案就是 `__sleep()` 和 `__wakeup()`

`__sleep()` 在 serialize() 被调用时被检查调用（如果存在）

`__wakeup()` 在 unserialize() 被调用时被检查调用（如果存在）。

`__sleep()` 可以用于清理对象，并返回一个包含对象中所有应被序列化的变量名称的数组。如果该方法未返回任何内容，则 NULL 被序列化，并产生一个 E_NOTICE 级别的错误。

`__wakeup()` 经常用在反序列化操作中，例如重新建立数据库连接，或执行其它初始化操作。

使用代码示例：

```php
<?php

class Connection
{
    protected $link;
    private $server, $username, $password, $db;

    public function __construct($server, $username, $password, $db)
    {
        $this->server = $server;
        $this->username = $username;
        $this->password = $password;
        $this->db = $db;
        $this->connect();
    }

    private function connect()
    {
        $this->link = mysql_connect($this->server, $this->username, $this->password);
        mysql_select_db($this->db, $this->link);
    }

    public function __sleep()
    {
        return array('server', 'username', 'password', 'db');
    }

    public function __wakeup()
    {
        $this->connect();
    }
}
```





#### `__toString()`

`__toString()` 方法用于一个类被当成字符串时应怎样回应。

例如 echo $obj;  应该显示些什么。此方法必须返回一个字符串，否则将发出一条 E_RECOVERABLE_ERROR 级别的致命错误。

这在代码调试中非常有用，只要针对类编写好相应的 `toString()` 方法，就是方便地直接 echo 对象来了解对象的结构，包括存在哪些属性及属性值等。

使用代码示例：

```php
<?php
class TestClass
{
    public $foo;

    public function __construct($foo)
    {
        $this->foo = $foo;
    }

    public function __toString()
    {
        return $this->foo;
    }
}

$class = new TestClass('Hello');
echo $class;

// 输出结果：
Hello
```



#### `__invoke()`

当一个对象被当成函数方法使用时，就会触发__invoke()方法，可带参数，使用形式好像有点像构造函数，并且可带参数列表。我也没有过具体的应用场景的使用，在某些需求中可能会很有用。

使用代码示例：

````php
<?php

class CallableClass
{
    public function __invoke($x)
    {
        var_dump($x);
    }
}

$obj = new CallableClass;
var_dump(is_callable($obj));
$obj(5);

// 输出结果：
true
5
````

**注意：可以使用 is_callable($obj) 来判断对象是否实现了__invoke()的魔术方法。**



#### `__clone()`

`__clone()` 这个魔术方法设计的目的就是为了实现类的深拷贝。当我们使用 `newObj = obj` 克隆对象时，实际上只是实现了浅拷贝，浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。这显然不是我们想要的，这时候就可以借助 `__clone()` 来实现深拷贝。

使用代码示例：

```php
<?php

class SubObject
{
    static $instances = 0;
    public $instance;

    public function __construct()
    {
        $this->instance = ++self::$instances;
    }

    public function __clone()
    {
        $this->instance = ++self::$instances;
    }
}

class MyCloneable
{
    public $object1;
    public $object2;

    function __clone()
    {
        // 强制复制一份this->object， 否则仍然指向同一个对象
        $this->object1 = clone $this->object1;
        $this->object2 = clone $this->object2;
    }
}

$obj = new MyCloneable();
$obj->object1 = new SubObject();
$obj->object2 = new SubObject();

$obj2 = clone $obj;

echo "<pre>";

print("Original Object:\n");
print_r($obj);

print("Cloned Object:\n");
print_r($obj2);

// 输出结果：
Original Object:
MyCloneable Object
(
    [object1] => SubObject Object
    (
    [instance] => 1
    )

    [object2] => SubObject Object
    (
        [instance] => 2
    )
)
    
Cloned Object:
MyCloneable Object
(
    [object1] => SubObject Object
    (
    	[instance] => 3
    )
    
    [object2] => SubObject Object
    (
        [instance] => 4
    )
)
```

当复制完成时，如果定义了 `__clone()`  方法，则新创建的对象（复制生成的对象）中的 `__clone()`  方法会被调用，可用于修改属性的值（如果有必要的话）。



#### `__set_state()`

我们可以用 var_export() 来输出或返回一个变量的字符串表示，当调用 var_export() 导出类时，此静态方法会被调用（必须有）。

使用代码示例：

```php
<?php

 class A
 {
     public $var1;
     public $var2;

     public static function __set_state($an_array) // As of PHP 5.1.0
     {
         $obj = new A;
         $obj->var1 = $an_array['var1'];
         $obj->var2 = $an_array['var2'];
         return $obj;
     }
 }

 $a = new A;
 $a->var1 = 5;
 $a->var2 = 'foo';

 eval('$b = ' . var_export($a, true) . ';'); 
 var_dump($b);

// 输出结果：
object(A)[2]
public ‘var1’ => int 5
public ‘var2’ => string ‘foo’ (length=3)
```



#### `__debuginfo()`

当调用 var_dump($obj) 输出对象时，对象所属的类中的`__debuginfo()`魔术方法会被调用，如果该类中没有定义这个魔术方法，则默认输出对象的所有属性。

注意：这个特性在PHP5.6.0中被添加，所以低版本的PHP环境中是无法使用这个方法的。

使用实例代码：

````php
<?php
class C {
    private $prop;

    public function __construct($val) {
        $this->prop = $val;
    }

    public function __debugInfo() {
        return [
            'propSquared' => $this->prop ** 2,
        ];
    }
}

var_dump(new C(42));

// 输出结果：
object(C)#1 (1) { [“propSquared”]=> int(1764) }
````

很多人在工作或项目中基本没怎么用到魔术方法，学习的时候也仅仅局限于学习，也没有思考怎么去用(我自己就是)。

其实不仅仅是框架设计，在项目的一些业务逻辑实现、代码调试跟踪等方面，如果能充分魔术方法提供的特性，对代码的优化和实现的简洁优雅很有帮助，一些应用场景你可能自己去写代码实现逻辑非常复杂，但如果用得上魔术方法，有时候问题就迎刃而解。