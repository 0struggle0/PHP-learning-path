# PHP 错误处理

## 一、常见的错误类型

在PHP中，错误用于指出语法、环境或编程问题。根据错误出现在编程过程中的不同环节，大致可以分为以下**4**类。

![1593677178977](C:\Users\zzh\AppData\Roaming\Typora\typora-user-images\1593677178977.png)

1. 语法错误：是指编写的代码不符合PHP的语法规范。
   特点：语法错误最常见，也最容易修复
   例如：遗漏了一个分号，就会显示错误信息。这类错误会阻止PHP脚本执行，通常发生在程序开发时，可以通过错误报告进行修复，再重新运行检查。
2. 运行错误：一般不会阻止PHP脚本的执行，但会导致程序出现潜在的问题。
   例如：在一个脚本中定义了两次同名常量，PHP通常会在第二次定义时提示一条错误信息。虽然PHP脚本继续执行，但第二次定义常量的操作没有执行成功。
3. 逻辑错误：最让人头疼，不但不会阻止PHP脚本的执行，也不会显示出错误信息
   例如：在if语句中判断两个变量的值是否相等，如果错把比较运算符“==”写成赋值运算符“=”就是一种逻辑错误，很难被发现。
4. 环境错误：是由于PHP开发环境配置的问题引起的代码报错
   例如：用mb_strlen()这个函数时，如果PHP环境中没有启用mbstring扩展，就会导致程序出错。



## 二、错误级别

| 值    | 常量                                                         | 说明                                                         | 备注                                                         |
| ----- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1     | **E_ERROR**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 致命的运行时错误。这类错误一般是不可恢复的情况，例如内存分配导致的问题。后果是导致脚本终止不再继续运行。 |                                                              |
| 2     | **E_WARNING**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 运行时警告 (非致命错误)。仅给出提示信息，但是脚本不会终止运行。 |                                                              |
| 4     | **E_PARSE**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 编译时语法解析错误。解析错误仅仅由分析器产生。               |                                                              |
| 8     | **E_NOTICE**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 运行时通知。表示脚本遇到可能会表现为错误的情况，但是在可以正常运行的脚本里面也可能会有类似的通知。 |                                                              |
| 16    | **E_CORE_ERROR**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 在PHP初始化启动过程中发生的致命错误。该错误类似       **E_ERROR**，但是是由PHP引擎核心产生的。 | since PHP 4                                                  |
| 32    | **E_CORE_WARNING**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | PHP初始化启动过程中发生的警告 (非致命错误) 。类似 **E_WARNING**，但是是由PHP引擎核心产生的。 | since PHP 4                                                  |
| 64    | **E_COMPILE_ERROR**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 致命编译时错误。类似**E_ERROR**,       但是是由Zend脚本引擎产生的。 | since PHP 4                                                  |
| 128   | **E_COMPILE_WARNING**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 编译时警告 (非致命错误)。类似       **E_WARNING**，但是是由Zend脚本引擎产生的。 | since PHP 4                                                  |
| 256   | **E_USER_ERROR**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 用户产生的错误信息。类似       **E_ERROR**, 但是是由用户自己在代码中使用PHP函数 [trigger_error()](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/function.trigger-error.html)来产生的。 | since PHP 4                                                  |
| 512   | **E_USER_WARNING**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 用户产生的警告信息。类似       **E_WARNING**, 但是是由用户自己在代码中使用PHP函数 [trigger_error()](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/function.trigger-error.html)来产生的。 | since PHP 4                                                  |
| 1024  | **E_USER_NOTICE**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 用户产生的通知信息。类似       **E_NOTICE**, 但是是由用户自己在代码中使用PHP函数 [trigger_error()](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/function.trigger-error.html)来产生的。 | since PHP 4                                                  |
| 2048  | **E_STRICT**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 启用 PHP 对代码的修改建议，以确保代码具有最佳的互操作性和向前兼容性。 | since PHP 5                                                  |
| 4096  | **E_RECOVERABLE_ERROR**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 可被捕捉的致命错误。 它表示发生了一个可能非常危险的错误，但是还没有导致PHP引擎处于不稳定的状态。 如果该错误没有被用户自定义句柄捕获 (参见       [set_error_handler()](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/function.set-error-handler.html))，将成为一个 **E_ERROR**　从而脚本会终止运行。 | since PHP 5.2.0                                              |
| 8192  | **E_DEPRECATED**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 运行时通知。启用后将会对在未来版本中可能无法正常工作的代码给出警告。 | since PHP 5.3.0                                              |
| 16384 | **E_USER_DEPRECATED**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | 用户产少的警告信息。 类似       **E_DEPRECATED**, 但是是由用户自己在代码中使用PHP函数 [trigger_error()](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/function.trigger-error.html)来产生的。 | since PHP 5.3.0                                              |
| 30719 | **E_ALL**       ([integer](mk:@MSITStore:C:\Users\zzh\Desktop\PHP7中文手册(2018).chm::/res/language.types.integer.html)) | **E_STRICT**出外的所有错误和警告信息。                       | 30719 in PHP 5.3.x,      6143 in PHP 5.2.x,      2047 previously |

**常见的错误类型：**

1、Notice（E_NOTICE）

​	**使用未定义的变量**；

​	**访问不存在的数组元素等**

2、Warning（E_WARNING）

​	Warning错误级别相比Notice更严重一些，但是不会影响脚本继续执行。

​	**除零错误**（除法运算前，可以使用if判断除数是否为0，若为0则拒绝执行除法运算）；

​	**使用include包含不存在的文件**（使用include前，先用is_file()函数判断该文件是否存在，防止错误发生）；

3、Fatal error（E_ERROR）

​	Fatal error是一种致命错误，在运行时发生。一旦发生该错误，PHP脚本会立即停止执行。

​	**调用一个不存在的方法**；

4、Parse error（E_PARSE）

​	Parse error是语法解析错误，当脚本存在语法错误时，无法解析成功，就会发生此错误。

​	**漏写了分号**；



## 三、自定义默认错误处理程序

在 PHP 中，默认的错误处理很简单。一条错误消息会被发送到浏览器，这条消息带有文件名、行号以及描述错误的消息。

![1593673357920](C:\Users\zzh\AppData\Roaming\Typora\typora-user-images\1593673357920.png)

但是这样的报错方式在生产环境是不允许出现的，会影响用户体验，所以在生产环境我们通常会这样设置：

```php
// error_reporting用于设置报告的错误级别
// display_errors用于设置是否显示错误信息

1. 直接配置php.ini文件来显示错误报告
	display_errors = off 
	error_reporting = E_ALL & ~E_NOTICE; // (表示报告除E_NOTICE之外的所有级别的错误);

2. 通过 error_reporting() 和 ini_set() 函数
	ini_set('display_errors', 0);  // 即display_errors = off;
	error_reporting(E_ALL & ~E_NOTICE) // 设置应该报告何种 PHP 错误;
```

这样虽然把页面上的错误隐藏掉，让用户看不到这些问题；但是导致的问题就是，当我们要复现缺陷的时候，也看不到错误信息，就无从下手，只能猜测。

所以我们需要设置一下错误处理的方式，让错误既报出来，又不报到页面上，影响用户；这时就需要创建自定义错误处理函数。即错误日志功能。

我们创建了一个专用函数，可以在 PHP 中发生错误时调用该函数。

```php
function systemError($errorLevel, $errorMessage)
{
    echo "<b>Error:</b> [$errorLevel] $errorMessage"; // 一般都写到日志中
}
```

当它被触发时，它会取得错误级别和错误消息，然后输出错误级别和消息(在生产环境中，肯定只能输出到日志文件中)。

## 设置错误处理程序

PHP 的默认错误处理程序是内部的错误处理程序，要想针对所有错误来使用我们自定义的错误处理程序，只能把系统内部的处理程序替换掉，可以用下面的函数：

```php
set_error_handler("systemError");
# 如果你只需要处理特定错误级别的错误，可以给 set_error_handler() 添加第二个参数来规定错误级别。
```

## 实例

通过尝试输出不存在的变量，来测试这个错误处理程序：

```php
<?php
function systemError($errorLevel, $errorMessage)
{
    echo "<b>Error:</b> [$errorLevel] $errorMessage";
}

// 设置错误处理函数
set_error_handler("systemError");

// 触发错误
echo($test);
```

以上代码的输出如下所示：

```
Error: [8] Undefined variable: test
```

## 错误处理程序可接受的参数列表

设置的自定义函数可以接受最多五个参数：

| 参数          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| error_level   | 必需。为用户定义的错误规定错误报告级别。必须是一个数字。参见下面的表格：错误报告级别的值。 |
| error_message | 必需。为用户定义的错误规定错误消息。                         |
| error_file    | 可选。规定错误发生的文件名。                                 |
| error_line    | 可选。规定错误发生的行号。                                   |
| error_context | 可选。规定一个数组，包含了当错误发生时在用的每个变量以及它们的值（超全局数据\$_GET、\$\_POST等）。 |

## 错误报告级别

这些错误报告级别是用户自定义的错误处理程序处理的不同类型的错误：

| 值   | 常量                | 描述                                                         |
| :--- | :------------------ | :----------------------------------------------------------- |
| 2    | E_WARNING           | 非致命的 run-time 错误。不暂停脚本执行。                     |
| 8    | E_NOTICE            | run-time 通知。在脚本发现可能有错误时发生，但也可能在脚本正常运行时发生。 |
| 256  | E_USER_ERROR        | 致命的用户生成的错误。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_ERROR。 |
| 512  | E_USER_WARNING      | 非致命的用户生成的警告。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_WARNING。 |
| 1024 | E_USER_NOTICE       | 用户生成的通知。这类似于程序员使用 PHP 函数 trigger_error() 设置的 E_NOTICE。 |
| 4096 | E_RECOVERABLE_ERROR | 可捕获的致命错误。类似 E_ERROR，但可被用户定义的处理程序捕获。（参见 set_error_handler()） |
| 8191 | E_ALL               | 所有错误和警告。（在 PHP 5.4 中，E_STRICT 成为 E_ALL 的一部分） |

## 手动触发错误

在程序中，当判断用户的输入无效时，我们可以选择由 trigger_error() 函数触发错误来改变程序走向。

```php
<?php
$test = 2;
if ($test > 1) {
    trigger_error("变量值必须小于等于 1");
}
```

可以在脚本中任何位置触发错误，通过添加的第二个参数，能够规定所触发的错误类型。

可能的错误类型：

- E_USER_ERROR - 致命的用户生成的 run-time 错误。错误无法恢复。脚本执行被中断。
- E_USER_WARNING - 非致命的用户生成的 run-time 警告。脚本执行不被中断。
- E_USER_NOTICE - 默认。用户生成的 run-time 通知。在脚本发现可能有错误时发生，但也可能在脚本正常运行时发生。

## 实例

在本例中，如果 "test" 变量大于 "1"，则发生 E_USER_WARNING 错误。如果发生了 E_USER_WARNING，我们将使用我们自定义的错误处理程序并结束脚本：

```php
<?php
function systemError($errorLevel, $errorMessage)
{
    echo "<b>Error:</b> [$errorLevel] $errorMessage";
}

// 设置错误处理函数
set_error_handler("systemError", E_USER_WARNING);

// 触发错误
$test = 2;
if ($test > 1) {
    trigger_error("变量值必须小于等于 1", E_USER_WARNING);
}
?>
```

以上代码的输出如下所示：

```php
Error: [512] 变量值必须小于等于 1
脚本结束
```