---
title: JavaScript流程控制与错误处理
date: 2019-06-15 11:31:38
tags: [switch, throw, try...catch, promise]
categories: JavaScript
---
<center>if...else, switch, throw, try...catch, promise</center>
<!-- more -->

# 语句块

语句块由一对大括号界定

```
{
   statement_1;   
   statement_2;
   statement_3;
   .
   .
   .
   statement_n;
}
```

语句块通常用于流程控制，如`if`,`for`,`while`等

```
while (x < 10) {
  x++;
}
```

这里`x++`就是一个语句块。

**重要：**在es6之前，Javascript没有块作用域。在一个块中引入的变量的作用域是**包含函数或脚本**。
es6，使用`let`和`const`的变量是块作用域的。

# 条件判断语句

## if...else语句

逻辑条件为真，用`if`执行一个语句；逻辑条件为假，使用可选择的`else`从句执行语句。

falsy values: 
- `false`
- `undefined`
- `none`
- `NaN`
- `0`
- 空字符串`''`

```
if (condition_1) {
  statement_1;
}else if (condition_2) {
  statement_2;
}else if (condition_n_1) {
  statement_n;
}else {
  statement_last;
}
```

不要混淆原始布尔值`true`和`false`与`Boolean`对象的真和假。例如：

```
var b = new Boolean(false)
if(b) //结果视为真
if(b == true) //结果视为假
```

## switch语句

一个程序求一个表达式的值，尝试匹配表达式的值到一个`case`标签，如果匹配成功，这个程序执行相关语句。

```
switch (expression) {
   case label_1:
      statements_1
      break;
   case label_2:
      statements_2
      break;
   ...
   default:
      statements_def
      break;
}
```

如果没有匹配的`case`语句，找`default`语句；如果没有`default`语句，程序继续执行`switch`语句后面的语句。

`default`语句通常在switch语句最后面，不是必须。

`可选的break语句`与每个`case`语句相关联，保证匹配的语句执行完后跳出`switch`语句。
如果`break`语句被忽略，程序继续执行switch语句中的下一条语句。

# 异常处理语句

用`throw`语句抛出一个错误，用`try...catch`语句捕获处理它。

## throw语句

使用`throw`语句抛出一个异常（抛出一个含有值得表达式）
可以抛出任意表达式而不是某种类型的表达式。

```
throw "Error2";   // String type
throw 42;         // Number type
throw true;       // Boolean type
throw {toString: function() { return "I'm an object!"; } };
```

## try...catch语句

- `try...catch`语句标记一块待尝试的语句，并规定一个以上的响应应该有一个异常被抛出。
- 如果抛出一个异常，`try...catch`语句就捕获它。
- `try...catch`语句有**一个**包含一句或多条语句的`try代码块`。
- `try...catch`语句有**0个或多个**`catch代码块`。
- `catch`代码块的语句会在`try`代码块中抛出异常时执行。
- 如果`try`代码块中的语句（或者try 代码块中调用的方法）一旦抛出了异常，那么执行流程会立即进入`catch`代码块。
- 如果`try`代码块没有抛出异常，`catch`代码块就会被跳过。
- `finally`代码块总会紧跟在`try`和`catch`代码块之后执行，但会在`try`和`catch`代码块之后的其他代码之前执行。

```
function getMonthName(mo) {
  mo = mo - 1; // Adjust month number for array index (1 = Jan, 12 = Dec)
  var months = ["Jan","Feb","Mar","Apr","May","Jun","Jul",
                "Aug","Sep","Oct","Nov","Dec"];
  if (months[mo]) {
    return months[mo];
  } else {
    throw "InvalidMonthNo"; //throw keyword is used here
  }
}

try { // statements to try
  monthName = getMonthName(myMonth); // function could throw exception
}
catch (e) {
  monthName = "unknown";
  logMyErrors(e); // pass exception object to error handler -> your own function
}
```

### catch块

可以使用`catch`块处理所有`try`块中产生的异常

```
catch (catchID) {
  statements
}
```

- 捕捉块指定了一个标识符（上述语句中的catchID）来存放抛出语句指定的值；
- 可以用这个标识符来获取抛出的异常信息。
- 在插入throw块时JavaScript创建这个标识符；
- 标识符只存在于catch块的存续期间里；
- 当catch块执行完成时，标识符不再可用。

### finally块

- `finally`块包含了在try和catch块完成后、下面接着try...catch的语句之前执行的语句。
- `finally`块无论是否抛出异常都会执行。如果抛出了一个异常，就算没有异常处理，finally块里的语句也会执行。

如果`finally`块返回一个值，该值会是整个try-catch-finally流程的返回值，不管在try和catch块中语句返回了什么,也就是finally块返回值覆盖try/catch块返回值：

```
function f() {
  try {
    console.log(0);
    throw "bogus";
  } catch(e) {
    console.log(1);
    return true; // this return statement is suspended
                 // until finally block has completed
    console.log(2); // not reachable
  } finally {
    console.log(3);
    return false; // overwrites the previous "return"
    console.log(4); // not reachable
  }
  // "return false" is executed now  
  console.log(5); // not reachable
}
f(); // console 0, 1, 3; returns false
```

用`finally`块覆盖返回值也适用于在catch块内抛出或重新抛出的异常：

```
function f() {
  try {
    throw 'bogus';
  } catch(e) {
    console.log('caught inner "bogus"');
    throw e; // this throw statement is suspended until 
             // finally block has completed
  } finally {
    return false; // overwrites the previous "throw"
  }
  // "return false" is executed now
}

try {
  f();
} catch(e) {
  // this is never reached because the throw inside
  // the catch is overwritten
  // by the return in finally
  console.log('caught outer "bogus"');
}

// OUTPUT
// caught inner "bogus"
```

# Promise

从es6开始，JavaScript增加了对`Promise`对象的支持。对延时和异步操作流进行控制。

```
function imgLoad(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    request.open('GET', url);
    request.responseType = 'blob';
    request.onload = function() {
      if (request.status === 200) {
        resolve(request.response);
      } else {
        reject(Error('Image didn\'t load successfully; error code:' 
                     + request.statusText));
      }
    };
    request.onerror = function() {
      reject(Error('There was a network error.'));
    };
    request.send();
  });
}
```

[MDN Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

[MDN 流程控制与错误处理](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Control_flow_and_error_handling)

