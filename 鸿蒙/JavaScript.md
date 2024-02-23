## JavaScript

### JavaScript变量

#### 声明（创建）

使用**`var`**关键字来“**声明**”变量，声明之后该变量是空的（他没有值）。



#### Value = undefined

在计算机程序中，经常会声明无值的变量。未使用值来声明的变量，其值实际上是 undefined。

在执行过以下语句后，变量 carname 的值将是 undefined：

```js
var carname;
```



#### 重新声明JavaScript

如果重新声明 JavaScript 变量，该变量的值不会丢失：

在以下两条语句执行后，变量 carname 的值依然是 "Volvo"：

```js
var carname="Volvo";
var carname;
```





### JavaScript let和const

ES6新增了两个关键字：**`let`**和**`const`**

**let 声明的变量只在 let 命令所在的代码块内有效。**

**const 声明一个只读的常量，一旦声明，常量的值就不能改变。**

在 ES6 之前，JavaScript 只有两种作用域： **全局变量** 与 **函数内的局部变量**。



#### 块级作用域（Block Scope）

使用 var 关键字声明的变量不具备块级作用域的特性，它在 {} 外依然能被访问到。

```js
{ 
    var x = 2; 
}
// 这里可以使用 x 变量
```

在 ES6 之前，是没有块级作用域的概念的。

ES6 可以使用 let 关键字来实现块级作用域。

let 声明的变量只在 let 命令所在的代码块 **{}** 内有效，在 **{}** 之外不能访问。

```js
{ 
    let x = 2;
}
// 这里不能使用 x 变量
```



#### 重新定义变量

使用 var 关键字重新声明变量可能会带来问题。

在块中重新声明变量也会重新声明块外的变量：

```js
var x = 10;
// 这里输出 x 为 10
{ 
    var x = 2;
    // 这里输出 x 为 2
}
// 这里输出 x 为 2
```

let 关键字就可以解决这个问题，因为它只在 let 命令所在的代码块 **{}** 内有效。

```js
var x = 10;
// 这里输出 x 为 10
{ 
    let x = 2;
    // 这里输出 x 为 2
}
// 这里输出 x 为 10
```



#### 循环作用域

使用 var 关键字：

```js
var i = 5;
for (var i = 0; i < 10; i++) {
    // 一些代码...
}
// 这里输出 i 为 10
```

使用 let 关键字：

```js
var i = 5;
for (let i = 0; i < 10; i++) {
    // 一些代码...
}
// 这里输出 i 为 5
```

在第一个实例中，使用了 **var** 关键字，它声明的变量是全局的，包括循环体内与循环体外。

在第二个实例中，使用 **let** 关键字， 它声明的变量作用域只在循环体内，循环体外的变量不受影响。



#### 局部变量

在函数体内使用 **var** 和 **let** 关键字声明的变量有点类似。

它们的作用域都是 **局部的**:

```js
// 使用 var
function myFunction() {
    var carName = "Volvo";   // 局部作用域
}

// 使用 let
function myFunction() {
    let carName = "Volvo";   //  局部作用域
}
```



#### 全局变量

在函数体外或代码块外使用 **var** 和 **let** 关键字声明的变量也有点类似。

它们的作用域都是 **全局的**:

```js
// 使用 var
var x = 2;       // 全局作用域

// 使用 let
let x = 2;       // 全局作用域
```



#### HTML 代码中使用全局变量

在 JavaScript 中, 全局作用域是针对 JavaScript 环境。

在 HTML 中, 全局作用域是针对 window 对象。

使用 **var** 关键字声明的全局作用域变量属于 window 对象:

```js
var carName = "Volvo";
// 可以使用 window.carName 访问变量
```

使用 **let** 关键字声明的全局作用域变量不属于 window 对象

```js
let carName = "Volvo";
// 不能使用 window.carName 访问变量
```



#### 变量提升

var关键字定义的变量可以使用后声明，也就是变量可以先使用再声明。

```js
// 在这里可以使用 carName 变量
 
var carName;
```



#### const 关键字

const 用于声明一个或多个常量，声明时必须进行初始化，且初始化后值不可再修改：

```js
const PI = 3.141592653589793;
PI = 3.14;      // 报错
PI = PI + 10;   // 报错
```

`const`定义常量与使用`let` 定义的变量相似：

- 二者都是块级作用域
- 都不能和它所在作用域内的其他变量或函数拥有相同的名称

两者还有以下两点区别：

- `const`声明的常量必须初始化，而`let`声明的变量不用
- const 定义常量的值不能通过再赋值修改，也不能再次声明。而 let 定义的变量值可以修改。



#### 并非真正的常量

const 的本质: const 定义的变量并非常量，并非不可变，它定义了一个常量引用一个值。使用 const 定义的对象或者数组，其实是可变的。下面的代码并不会报错：

```js
// 创建常量对象
const car = {type:"Fiat", model:"500", color:"white"};
// 修改属性:
car.color = "red";
// 添加属性
car.owner = "Johnson";


// 创建常量数组
const cars = ["Saab", "Volvo", "BMW"];
// 修改元素
cars[0] = "Toyota";
// 添加元素
cars.push("Audi");
```







#### 重置变量

> 使用var关键字声明的全局作用域变量属于window对象。
>
> 使用let关键字声明的全局作用域变量不属于window对象。
>
> 使用var关键字声明的变量在任何地方都可以修改。
>
> 在相同的作用域或块级作用域中，不能使用let关键字来重置var关键字声明的变量。
>
> 在相同的作用域或块级作用域中，不能使用let关键字来重置let关键字声明的变量。
>
> let关键字在不同作用域，或不用块级作用域中是可以重新声明赋值的。
>
> 在相同的作用域或块级作用域中，不能使用const关键字来重置var和let关键字声明的变量。
>
> 在相同的作用域或块级作用域中，不能使用const关键字来重置const关键字声明的变量
>
> const 关键字在不同作用域，或不同块级作用域中是可以重新声明赋值的:
>
> var关键字定义的变量可以先使用后声明。
>
> let关键字定义的变量需要先声明再使用。
>
> const关键字定义的常量，声明时必须进行初始化，且初始化后不可再修改。





### JavaScript 数据类型

**值类型(基本类型)**：字符串（String）、数字(Number)、布尔(Boolean)、空（Null）、未定义（Undefined）、Symbol。

**引用数据类型（对象类型）**：对象(Object)、数组(Array)、函数(Function)，还有两个特殊的对象：正则（RegExp）和日期（Date）。



### JavaScript类型转换

+ 通过使用JavaScript函数
+ 通过JavaScript自身自动转换

#### 数字转换为字符串

全局方法 **String()** 可以将数字转换为字符串。

该方法可用于任何类型的数字，字母，变量，表达式：

```js
String(x)         // 将变量 x 转换为字符串并返回
String(123)       // 将数字 123 转换为字符串并返回
String(100 + 23)  // 将数字表达式转换为字符串并返回
```

Number 方法 **toString()** 也是有同样的效果。

```js
x.toString()
(123).toString()
(100 + 23).toString()
```

在 [Number 方法](https://www.runoob.com/jsref/jsref-obj-number.html) 章节中，你可以找到更多数字转换为字符串的方法：

| 方法            | 描述                                                 |
| :-------------- | :--------------------------------------------------- |
| toExponential() | 把对象的值转换为指数计数法。                         |
| toFixed()       | 把数字转换为字符串，结果的小数点后有指定位数的数字。 |
| toPrecision()   | 把数字格式化为指定的长度。                           |



#### 将布尔值转换为字符串

全局方法 **String()** 可以将布尔值转换为字符串。

```js
String(false)     // 返回 "false"
String(true)     // 返回 "true"
```

Boolean 方法 **toString()** 也有相同的效果。

```js
false.toString()   // 返回 "false"
true.toString()   // 返回 "true"
```



#### 将日期转换为字符串

Date() 返回字符串。

```js
Date()      // 返回 Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)
```

全局方法 String() 可以将日期对象转换为字符串。

```js
String(new Date())      // 返回 Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)
```

Date 方法 **toString()** 也有相同的效果。

```js
obj = new Date()
obj.toString()   // 返回 Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)
```



#### 将字符串转换为数字

全局方法 **Number()** 可以将字符串转换为数字。

字符串包含数字(如 "3.14") 转换为数字 (如 3.14).

空字符串转换为 0。

其他的字符串会转换为 NaN (不是个数字)。

```js
Number("3.14")    // 返回 3.14
Number(" ")       // 返回 0 
Number("")        	// 返回 0
Number("99 88")   // 返回 NaN
```

在 [Number 方法](https://www.runoob.com/jsref/jsref-obj-number.html) 章节中，你可以查看到更多关于字符串转为数字的方法：

| 方法         | 描述                               |
| :----------- | :--------------------------------- |
| parseFloat() | 解析一个字符串，并返回一个浮点数。 |
| parseInt()   | 解析一个字符串，并返回一个整数。   |



#### 一元运算符 +

**Operator +** 可用于将变量转换为数字：

```js
var y = "5";   // y 是一个字符串
var x = + y;   // x 是一个数字
```

如果变量不能转换，它仍然会是一个数字，但值为 NaN (不是一个数字):

```js
var y = "John";  // y 是一个字符串
var x = + y;   // x 是一个数字 (NaN)
```



#### 将布尔值转换为数字

全局方法 **Number()** 可将布尔值转换为数字。

```js
Number(false)     // 返回 0
Number(true)      // 返回 1
```



#### 将日期转换为数字

全局方法 **Number()** 可将日期转换为数字。

```js
d = new Date();
Number(d)     // 返回 1404568027739
```

日期方法 **getTime()** 也有相同的效果。

```js
d = new Date();
d.getTime()    // 返回 1404568027739
```



#### 自动转换类型

当 JavaScript 尝试操作一个 "错误" 的数据类型时，会自动转换为 "正确" 的数据类型。

以下输出结果不是你所期望的：

```js
5 + null    // 返回 5			null 转换为 0
"5" + null  // 返回"5null"	null 转换为 "null"
"5" + 1     // 返回 "51"		1 转换为 "1"
"5" - 1     // 返回 4			"5" 转换为 5
```



#### 自动转换为字符串

当你尝试输出一个对象或一个变量时 JavaScript 会自动调用变量的 toString() 方法：

```js
document.getElementById("demo").innerHTML = myVar;

myVar = {name:"Fjohn"} // toString 转换为 "[object Object]"
myVar = [1,2,3,4]    // toString 转换为 "1,2,3,4"
myVar = new Date()   // toString 转换为 "Fri Jul 18 2014 09:08:55 GMT+0200"
```

数字和布尔值也经常相互转换：

```js
myVar = 123       // toString 转换为 "123"
myVar = true       // toString 转换为 "true"
myVar = false      // toString 转换为 "false"
```

下表展示了使用不同的数值转换为数字(Number), 字符串(String), 布尔值(Boolean):

| 原始值              | 转换为数字 | 转换为字符串      | 转换为布尔值 | 实例                                                         |
| :------------------ | :--------- | :---------------- | :----------- | :----------------------------------------------------------- |
| false               | 0          | "false"           | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_false) |
| true                | 1          | "true"            | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_true) |
| 0                   | 0          | "0"               | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_number_0) |
| 1                   | 1          | "1"               | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_number_1) |
| "0"                 | 0          | "0"               | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_0) |
| "000"               | 0          | "000"             | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_000) |
| "1"                 | 1          | "1"               | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_1) |
| NaN                 | NaN        | "NaN"             | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_nan) |
| Infinity            | Infinity   | "Infinity"        | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_infinity) |
| -Infinity           | -Infinity  | "-Infinity"       | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_infinity_minus) |
| ""                  | 0          | ""                | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_empty) |
| "20"                | 20         | "20"              | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_number) |
| "Runoob"            | NaN        | "Runoob"          | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_string_text) |
| [ ]                 | 0          | ""                | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_array_empty) |
| [20]                | 20         | "20"              | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_array_one_number) |
| [10,20]             | NaN        | "10,20"           | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_array_two_numbers) |
| ["Runoob"]          | NaN        | "Runoob"          | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_array_one_string) |
| ["Runoob","Google"] | NaN        | "Runoob,Google"   | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_array_two_strings) |
| function(){}        | NaN        | "function(){}"    | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_function) |
| { }                 | NaN        | "[object Object]" | true         | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_object) |
| null                | 0          | "null"            | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_null) |
| undefined           | NaN        | "undefined"       | false        | [尝试一下 »](https://www.runoob.com/try/try.php?filename=tryjs_type_convert_undefined) |





### 严格模式

为什么使用严格模式:

- 消除代码运行的一些不安全之处，保证代码运行的安全；
- 提高编译器效率，增加运行速度；
- 为未来新版本的Javascript做好铺垫。

"严格模式"体现了Javascript更合理、更安全、更严谨的发展方向，包括IE 10在内的主流浏览器，都已经支持它，许多大项目已经开始全面拥抱它。

另一方面，同样的代码，在"严格模式"中，可能会有不一样的运行结果；一些在"正常模式"下可以运行的语句，在"严格模式"下将不能运行。掌握这些内容，有助于更细致深入地理解Javascript，让你变成一个更好的程序员。

#### 严格模式的限制

**不允许使用未声明的变量：**

```js
"use strict";
x = 3.14;                // 报错 (x 未定义)
```

>  对象也是一个变量

```js
"use strict";
x = {p1:10, p2:20};      // 报错 (x 未定义)
```



**不允许删除变量或对象。**

```js
"use strict";
var x = 3.14;
delete x;                // 报错
```



**不允许删除函数。**

```js
"use strict";
function x(p1, p2) {}; 
delete x;                // 报错
```



**不允许变量重名:**

```js
"use strict";
function x(p1, p1) {};   // 报错
```



**不允许使用八进制:**

```js
"use strict";
var x = 010;             // 报错
```



**不允许使用转义字符:**

```js
"use strict";
var x = \010;            // 报错
```



**不允许对只读属性赋值:**

```js
"use strict";
var obj = {};
Object.defineProperty(obj, "x", {value:0, writable:false});
obj.x = 3.14;            // 报错
```



**不允许对一个使用getter方法读取的属性进行赋值**

```js
"use strict";
var obj = {get x() {return 0} };
obj.x = 3.14;            // 报错
```



**不允许删除一个不允许删除的属性：**

```js
"use strict";
delete Object.prototype; // 报错
```



**变量名不能使用 "eval" 字符串:**

```js
"use strict";
var eval = 3.14;         // 报错
```



**变量名不能使用 "arguments" 字符串:**

```js
"use strict";
var arguments = 3.14;    // 报错
```



**不允许使用以下这种语句:**

```js
"use strict";
with (Math){x = cos(2)}; // 报错
```



**由于一些安全原因，在作用域 eval() 创建的变量不能被调用：**

```js
"use strict";
eval ("var x = 2");
alert (x);               // 报错
```



**禁止this关键字指向全局对象。**

```js
function f(){
    return !this;
} 
// 返回false，因为"this"指向全局对象，"!this"就是false

function f(){ 
    "use strict";
    return !this;
} 
// 返回true，因为严格模式下，this的值为undefined，所以"!this"为true。
```

因此，使用构造函数时，如果忘了加new，this不再指向全局对象，而是报错。

```js
function f(){
    "use strict";
    this.a = 1;
};
f();// 报错，this未定义
```



#### 保留关键字

为了向将来Javascript的新版本过渡，严格模式新增了一些保留关键字：

- implements
- interface
- let
- package
- private
- protected
- public
- static
- yield