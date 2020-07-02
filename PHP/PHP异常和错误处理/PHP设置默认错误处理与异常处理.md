# PHP 错误处理

在 PHP 中，默认的错误处理很简单。一条错误消息会被发送到浏览器，这条消息带有文件名、行号以及描述错误的消息。

![1593673357920](C:\Users\zzh\AppData\Roaming\Typora\typora-user-images\1593673357920.png)

但是这样的报错方式在生产环境是不允许出现的，会影响用户体验，所以在生产环境我们通常会这样设置：

```php
ini_set('display_errors', 0);  即display_errors = off;
并通过 error_reporting() 设置应该报告何种 PHP 错误;
```

这样虽然把页面上的错误隐藏掉，让用户看不到这些问题；但是导致的问题就是，当我们要复现缺陷的时候，也看不到错误信息，就无从下手，只能猜测。

所以我们需要设置一下错误处理的方式，让错误既报出来，又不报到页面上，影响用户；这时就需要创建自定义错误处理函数。

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

## 参数列表

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

## 触发错误

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