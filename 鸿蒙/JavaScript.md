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





### JavaScript使用误区

#### 加法与连接注意事项

加法是两个数字相加，连接是两个字符串连接

JavaScript 的加法和连接都使用 + 运算符。

接下来我们可以通过实例查看两个数字相加及数字与字符串连接的区别：

```js
var x = 10 + 5;          // x 的结果为 15
var x = 10 + "5";        // x 的结果为 "105"
```

使用变量相加结果也不一致:

```js
var x = 10;
var y = 5;
var z = x + y;      // z 的结果为 15

var x = 10;
var y = "5";
var z = x + y;      // z 的结果为 "105"
```



#### 浮点型数据使用注意事项

JavaScript 中的所有数据都是以 64 位**浮点型数据(float)** 来存储。

所有的编程语言，包括 JavaScript，对浮点型数据的精确度都很难确定：

```js
var x = 0.1;
var y = 0.2;
var z = x + y      // z 的结果为 0.30000000000000004
if (z == 0.3)      // 返回 false
```

为解决以上问题，可以用整数的乘除法来解决：

```js
var z = (x * 10 + y * 10) / 10;    // z 的结果为 0.3
```

更多内容可以参考：[JavaScript 中精度问题以及解决方案](https://www.runoob.com/w3cnote/js-precision-problem-and-solution.html)



#### JavaScript 字符串分行

字符串断行需要使用反斜杠(\)，如下所示:

```js
var x = "Hello \
World!";
```



#### return语句使用

JavaScript 默认是在一行的末尾自动结束。

以下两个实例返回结果是一样的(一个有分号一个没有):

```js
function myFunction(a) {
    var power = 10 
    return a * power
}

function myFunction(a) {
    var power = 10;
    return a * power;
}
```

JavaScript 也可以使用多行来表示一个语句，也就是说一个语句是可以分行的。

以下实例返回相同的结果:

```js
function myFunction(a) {
    var
    power = 10; 
    return a * power;
}
```

> 注意：不用对 return 语句进行断行。由于 return 是一个完整的语句，所以 JavaScript 将关闭 return 语句。



#### 数组中使用

许多程序语言都允许使用名字来作为数组的索引。

使用名字来作为索引的数组称为关联数组(或哈希)。

JavaScript 不支持使用名字来索引数组，只允许使用数字索引。

```js
var person = [];
person[0] = "John";
person[1] = "Doe";
person[2] = 46;
var x = person.length;     // person.length 返回 3
var y = person[0];       // person[0] 返回 "John"
```

在 JavaScript 中, **对象** 使用 **名字作为索引**。

如果你使用名字作为索引，当访问数组时，JavaScript 会把数组重新定义为标准对象。

执行这样操作后，数组的方法及属性将不能再使用，否则会产生错误:

```js
var person = [];
person["firstName"] = "John";
person["lastName"] = "Doe";
person["age"] = 46;
var x = person.length;     // person.length 返回 0
var y = person[0];       // person[0] 返回 undefined
```



#### Undefined 不是 Null

在 JavaScript 中, **null** 用于对象, **undefined** 用于变量，属性和方法。

对象只有被定义才有可能为 null，否则为 undefined。

如果我们想测试对象是否存在，在对象还没定义时将会抛出一个错误。

错误的使用方式：

```js
if (myObj !== null && typeof myObj !== "undefined") 
```

正确的方式是我们需要先使用 typeof 来检测对象是否已定义：

```js
if (typeof myObj !== "undefined" && myObj !== null) 
```



#### 程序块作用域

在每个代码块中 JavaScript 不会创建一个新的作用域，一般各个代码块的作用域都是全局的。

以下代码的的变量 i 返回 10，而不是 undefined：

```js
for (var i = 0; i < 10; i++) {
  // some code
}
return i;
```



### JavaScript表单

#### JavaScript表单验证

HTML 表单验证可以通过 JavaScript 来完成。

以下实例代码用于判断表单字段(fname)值是否存在， 如果不存在，就弹出信息，阻止表单提交：

```js
function validateForm() {
    var x = document.forms["myForm"]["fname"].value;
    if (x == null || x == "") {
        alert("需要输入名字。");
        return false;
    }
}
```



### JavaScript this 关键字

面向对象语言中 this 表示当前对象的一个引用。

但在JavaScript中this不是固定不变的，它会随着执行环境的改变而改变。

+ 在方法中，this表示该方法所属的对象
+ 如果单独使用，this表示全局对象
+ 在函数中，this表示全局对象；在严格模式下，this是未定义的（undefined）
+ 在事件中，this表示接受事件的元素
+ 类似call()和apply()方法可以将this引用到任何对象

```js
var person = {
  firstName: "John",
  lastName : "Doe",
  id       : 5566,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

#### 方法中的this

在对象方法中，this指向他所在方法的对象。

在上面一个实例中，this表示person对象。

fullName方法所属的对象就是person。

```js
fullName : function() {
  return this.firstName + " " + this.lastName;
}
```

#### 单独使用this

单独使用this，则他指向全局（Global）对象。

在浏览器中，window就是该全局对象为[**object Window**]：

```js
var x = this;
```

严格模式下，如果单独使用，this 也是指向全局(Global)对象。

```js
"use strict";
var x = this;
```

#### 函数中使用this（默认）

在函数中，函数的所属者默认绑定到this上。

在浏览器中，window就是该全局对象为[**object Window**]:

```js
function myFunction() {
  return this;
}
```

严格模式下函数是没有绑定到this上，这时候this是**`undefined`**。

```js
"use strict";
function myFunction() {
  return this;
}
```

#### 事件中的this

在 HTML 事件句柄中，this 指向了接收事件的 HTML 元素：

```html
<button onclick="this.style.display='none'">
点我后我就消失了
</button>
```

#### 对象方法中绑定

下面实例中，this是person对象，person对象是函数的所有者：

```js
var person = {
  firstName  : "John",
  lastName   : "Doe",
  id         : 5566,
  myFunction : function() {
    return this;
  }
};
```

```js
var person = {
  firstName: "John",
  lastName : "Doe",
  id       : 5566,
  fullName : function() {
    return this.firstName + " " + this.lastName;
  }
};
```

说明: **this.firstName** 表示 **this** (person) 对象的 **firstName** 属性。

#### 显式函数绑定

在 JavaScript 中函数也是对象，对象则有方法，**`apply`** 和 **`call`** 就是函数对象的方法。这两个方法异常强大，他们允许切换函数执行的上下文环境（context），即 this 绑定的对象。

在下面实例中，当我们使用 person2 作为参数来调用 person1.fullName 方法时, **this** 将指向 person2, 即便它是 person1 的方法：

```js
var person1 = {
  fullName: function() {
    return this.firstName + " " + this.lastName;
  }
}
var person2 = {
  firstName:"John",
  lastName: "Doe",
}
person1.fullName.call(person2);  // 返回 "John Doe"
```



### JavaScript JSON

#### JSON字符串转换为JavaScript对象

通常我们从服务器中读取JSON数据，并在网页中显示数据。

简单起见，我们网页中直接设置JSON字符串：

首先，创建 JavaScript 字符串，字符串为 JSON 格式的数据：

```js
var text = '{ "sites" : [' +
'{ "name":"Runoob" , "url":"www.runoob.com" },' +
'{ "name":"Google" , "url":"www.google.com" },' +
'{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
```

然后，使用 JavaScript 内置函数 JSON.parse() 将字符串转换为 JavaScript 对象:

```js
var obj = JSON.parse(text);
```

最后，在你的页面中使用新的 JavaScript 对象：

```js
var text = '{ "sites" : [' +
    '{ "name":"Runoob" , "url":"www.runoob.com" },' +
    '{ "name":"Google" , "url":"www.google.com" },' +
    '{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
    
obj = JSON.parse(text);
document.getElementById("demo").innerHTML = obj.sites[1].name + " " + obj.sites[1].url;
```



#### 相关函数

| 函数                                                         | 描述                                           |
| :----------------------------------------------------------- | :--------------------------------------------- |
| [JSON.parse()](https://www.runoob.com/js/javascript-json-parse.html) | 用于将一个 JSON 字符串转换为 JavaScript 对象。 |
| [JSON.stringify()](https://www.runoob.com/js/javascript-json-stringify.html) | 用于将 JavaScript 值转换为 JSON 字符串。       |



### javascript:void(0) 含义

我们经常会使用到 **javascript:void(0)** 这样的代码，那么在 JavaScript 中 **javascript:void(0)** 代表的是什么意思呢？

**javascript:void(0)** 中最关键的是 **void** 关键字， **void** 是 JavaScript 中非常重要的关键字，该操作符指定要计算一个表达式但是不返回值。

语法格式如下：

```js
void func()
javascript:void func()
```

或者

```js
void(func())
javascript:void(func())
```

下面的代码创建了一个超级链接，当用户点击以后不会发生任何事。

```js
<a href="javascript:void(0)">单击此处什么也不会发生</a>
```

当用户链接时，void(0) 计算为 0，但 Javascript 上没有任何效果。

以下实例中，在用户点击链接后显示警告信息：

```js
<p>点击以下链接查看结果：</p>
<a href="javascript:void(alert('Warning!!!'))">点我!</a>
```

以下实例中参数 a 将返回 undefined :

```js
function getValue(){
   var a,b,c;
   a = void ( b = 5, c = 7 );
   document.write('a = ' + a + ' b = ' + b +' c = ' + c );
}
```

#### href="#"与href="javascript:void(0)"的区别

**#** 包含了一个位置信息，默认的锚是**#top** 也就是网页的上端。

而javascript:void(0), 仅仅表示一个死链接。

在页面很长的时候会使用 **#** 来定位页面的具体位置，格式为：**# + id**。

如果你要定义一个死链接请使用 javascript:void(0) 。

> ```js
> // 阻止链接跳转，URL不会有任何变化
> <a href="javascript:void(0)" rel="nofollow ugc">点击此处</a>
> 
> // 虽然阻止了链接跳转，但URL尾部会多个#，改变了当前URL。（# 主要用于配合 location.hash）
> <a href="#" rel="nofollow ugc">点击此处</a>
> 
> // 同理，# 可以的话，? 也能达到阻止页面跳转的效果，但也相同的改变了URL。（? 主要用于配合 location.search）
> <a href="?" rel="nofollow ugc">点击此处</a>
> 
> // Chrome 中即使 javascript:0; 也没变化，firefox中会变成一个字符串0
> <a href="javascript:0" rel="nofollow ugc">点击此处</a>
> ```
>
> 



### JavaScript异步编程

什么时候用异步编程

在前端编程中（甚至后端有时也是这样），我们在处理一些简短、快速的操作时，例如计算 1 + 1 的结果，往往在主线程中就可以完成。主线程作为一个线程，不能够同时接受多方面的请求。所以，当一个事件没有结束时，界面将无法处理其他请求。

现在有一个按钮，如果我们设置它的 onclick 事件为一个死循环，那么当这个按钮按下，整个网页将失去响应。

为了避免这种情况的发生，我们常常用子线程来完成一些可能消耗时间足够长以至于被用户察觉的事情，比如读取一个大文件或者发出一个网络请求。因为子线程独立于主线程，所以即使出现阻塞也不会影响主线程的运行。但是子线程有一个局限：一旦发射了以后就会与主线程失去同步，我们无法确定它的结束，如果结束之后需要处理一些事情，比如处理来自服务器的信息，我们是无法将它合并到主线程中去的。

为了解决这个问题，JavaScript 中的**异步操作函数往往通过回调函数来实现异步任务的结果处理。**

#### 回调函数

回调函数就是一个函数，它是在我们启动一个异步任务的时候就告诉它：等你完成了这个任务之后要干什么。这样一来主线程几乎不用关心异步任务的状态了，他自己会善始善终。

```js
function print() {
    document.getElementById("demo").innerHTML="RUNOOB!";
}
setTimeout(print, 3000);
```

这段程序中的 setTimeout 就是一个消耗时间较长（3 秒）的过程，它的第一个参数是个回调函数，第二个参数是毫秒数，这个函数执行之后会产生一个子线程，子线程会等待 3 秒，然后执行回调函数 "print"，在命令行输出 "RUNOOB!"。

当然，JavaScript 语法十分友好，我们不必单独定义一个函数 print ，我们常常将上面的程序写成：

```js
setTimeout(function () {
    document.getElementById("demo").innerHTML="RUNOOB!";
}, 3000);
```

**注意：**既然 setTimeout 会在子线程中等待 3 秒，在 setTimeout 函数执行之后主线程并没有停止，所以：

```js
setTimeout(function () {
    document.getElementById("demo1").innerHTML="RUNOOB-1!";  // 三秒后子线程执行
}, 3000);
document.getElementById("demo2").innerHTML="RUNOOB-2!";      // 主线程先执行
```

这段程序的执行结果是：

```
RUNOOB-1!    // 三秒后子线程执行
RUNOOB-2!    // 主线程先执行
```

#### 异步AJAX

除了setTimeout函数以外，异步回调广泛应用于AJAX编程。有关于AJAX详细请参见：https://www.runoob.com/ajax/ajax-tutorial.html

XMLHttpRequest 常常用于请求来自远程服务器上的 XML 或 JSON 数据。一个标准的 XMLHttpRequest 对象往往包含多个回调：

```js
var xhr = new XMLHttpRequest();
 
xhr.onload = function () {
    // 输出接收到的文字数据
    document.getElementById("demo").innerHTML=xhr.responseText;
}
 
xhr.onerror = function () {
    document.getElementById("demo").innerHTML="请求出错";
}
 
// 发送异步 GET 请求
xhr.open("GET", "https://www.runoob.com/try/ajax/ajax_info.txt", true);
xhr.send();
```

XMLHttpRequest 的 onload 和 onerror 属性都是函数，分别在它请求成功和请求失败时被调用。如果你使用完整的 jQuery 库，也可以更加优雅的使用异步 AJAX：

```js
$.get("https://www.runoob.com/try/ajax/demo_test.php",function(data,status){
    alert("数据: " + data + "\n状态: " + status);
});
```



### JavaScript Promise

在学习本章节内容前，你需要先了解什么是异步编程，可以参考：[JavaScript 异步编程](https://www.runoob.com/js/javascript-async.html)

Promise 是一个 ECMAScript 6 提供的类，目的是更加优雅地书写复杂的异步任务。

由于 Promise 是 ES6 新增加的，所以一些旧的浏览器并不支持，苹果的 Safari 10 和 Windows 的 Edge 14 版本以上浏览器才开始支持 ES6 特性。

#### 构建Promise

现在我们新建一个Promise对象：

```js
new Promise(function (resolve, reject) {
    // 要做的事情...
});
```

通过新建一个 Promise 对象好像并没有看出它怎样 "更加优雅地书写复杂的异步任务"。我们之前遇到的异步任务都是一次异步，如果需要多次调用异步函数呢？例如，如果我想分三次输出字符串，第一次间隔 1 秒，第二次间隔 4 秒，第三次间隔 3 秒：

```js
setTimeout(function () {
    console.log("First");
    setTimeout(function () {
        console.log("Second");
        setTimeout(function () {
            console.log("Third");
        }, 3000);
    }, 4000);
}, 1000);
```

这段程序实现了这个功能，但是它是用 "函数瀑布" 来实现的。可想而知，在一个复杂的程序当中，用 "函数瀑布" 实现的程序无论是维护还是异常处理都是一件特别繁琐的事情，而且会让缩进格式变得非常冗赘。

现在我们用 Promise 来实现同样的功能：

```js
new Promise(function (resolve, reject) {
    setTimeout(function () {
        console.log("First");
        resolve();
    }, 1000);
}).then(function () {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log("Second");
            resolve();
        }, 4000);
    });
}).then(function () {
    setTimeout(function () {
        console.log("Third");
    }, 3000);
});
```

#### Promise 的构造函数

Promise 构造函数是 JavaScript 中用于创建 Promise 对象的内置构造函数。

Promise 构造函数**接受一个函数作为参数，该函数是同步的并且会被立即执行，所以我们称之为起始函数**。**起始函数包含两个参数 `resolve` 和 `reject`**，分别表示 Promise 成功和失败的状态。

起始函数执行成功时，它应该**调用 `resolve` 函数并传递成功的结果**。当起始函数执行失败时，它应该**调用 `reject` 函数并传递失败的原因**。

Promise 构造函数返回一个 Promise 对象，该对象具有以下几个方法：

- then：用于处理 Promise 成功状态的回调函数。
- catch：用于处理 Promise 失败状态的回调函数。
- finally：无论 Promise 是成功还是失败，都会执行的回调函数。

下面是一个使用 Promise 构造函数创建 Promise 对象的例子：

**当 Promise 被构造时，起始函数会被同步执行**：

```js
const promise = new Promise((resolve, reject) => {
  // 异步操作
  setTimeout(() => {
    if (Math.random() < 0.5) {
      resolve('success');
    } else {
      reject('error');
    }
  }, 1000);
});
 
promise.then(result => {
  console.log(result);
}).catch(error => {
  console.log(error);
});
```

在上面的例子中，我们使用 Promise 构造函数创建了一个 Promise 对象，并使用 setTimeout 模拟了一个异步操作。如果异步操作成功，则调用 resolve 函数并传递成功的结果；如果异步操作失败，则调用 reject 函数并传递失败的原因。然后我们使用 then 方法处理 Promise 成功状态的回调函数，使用 catch 方法处理 Promise 失败状态的回调函数。

这段程序会直接输出 **error** 或 **success**。

resolve 和 reject 都是函数，其中调用 resolve 代表一切正常，reject 是出现异常时所调用的：

```js
new Promise(function (resolve, reject) {
    var a = 0;
    var b = 1;
    if (b == 0) reject("Divide zero");
    else resolve(a / b);
}).then(function (value) {
    console.log("a / b = " + value);
}).catch(function (err) {
    console.log(err);
}).finally(function () {
    console.log("End");
});
```

这段代码的执行结果是：

```js
a / b = 0
End
```

Promise 类有 .then() .catch() 和 .finally() 三个方法，这三个方法的参数都是一个函数，.then() 可以将参数中的函数添加到当前 Promise 的正常执行序列，.catch() 则是设定 Promise 的异常处理序列，.finally() 是在 Promise 执行的最后一定会执行的序列。 .then() 传入的函数会按顺序依次执行，有任何异常都会直接跳到 catch 序列：

```js
new Promise(function (resolve, reject) {
    console.log(1111);
    resolve(2222);
}).then(function (value) {
    console.log(value);
    return 3333;
}).then(function (value) {
    console.log(value);
    throw "An error";
}).catch(function (err) {
    console.log(err);
});
```

执行结果：

```js
1111
2222
3333
An error
```

resolve() 中可以放置一个参数用于向下一个 then 传递一个值，then 中的函数也可以返回一个值传递给 then。但是，**如果 `then` 中返回的是一个 Promise 对象，那么下一个 then 将相当于对这个返回的 Promise 进行操作**，这一点从刚才的计时器的例子中可以看出来。

reject() 参数中一般会传递一个异常给之后的 catch 函数用于处理异常。

但是请注意以下两点：

- resolve 和 reject 的**作用域只有起始函数**，不包括 then 以及其他序列；
- resolve 和 reject 并不能够使起始函数停止运行，**别忘了 return**。



#### Promise函数

上述的“计数器”程序看起来比函数瀑布还要长，所以我们可以将它的核心部分写成一个Promise函数：

```js
function print(delay, message) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log(message);
            resolve();
        }, delay);
    });
}
```

然后我们就可以放心大胆的实现程序功能了：

```js
print(1000, "First").then(function () {
    return print(4000, "Second");
}).then(function () {
    print(3000, "Third");
});
```

这种返回值为一个 Promise 对象的函数称作 Promise 函数，它常常用于开发基于异步操作的库。

#### 回答常见的问题（FAQ）

**Q: then、catch 和 finally 序列能否顺序颠倒？**

A: 可以，效果完全一样。但不建议这样做，最好按 then-catch-finally 的顺序编写程序。

**Q: 除了 then 块以外，其它两种块能否多次使用？**

A: 可以，finally 与 then 一样会按顺序执行，但是 catch 块只会执行第一个，除非 catch 块里有异常。所以最好只安排一个 catch 和 finally 块。

**Q: then 块如何中断？**

A: then 块默认会向下顺序执行，return 是不能中断的，可以通过 throw 来跳转至 catch 实现中断。

**Q: 什么时候适合用 Promise 而不是传统回调函数？**

A: 当需要多次顺序执行异步操作的时候，例如，如果想通过异步方法先后检测用户名和密码，需要先异步检测用户名，然后再异步检测密码的情况下就很适合 Promise。

**Q: Promise 是一种将异步转换为同步的方法吗？**

A: 完全不是。Promise 只不过是一种更良好的编程风格。

**Q: 什么时候我们需要再写一个 then 而不是在当前的 then 接着编程？**

A: 当你又需要调用一个异步任务的时候。



#### 异步函数

异步函数（async function）是 ECMAScript 2017 (ECMA-262) 标准的规范，几乎被所有浏览器所支持，除了 Internet Explorer。

在 Promise 中我们编写过一个 Promise 函数：

```js
function print(delay, message) {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            console.log(message);
            resolve();
        }, delay);
    });
}
```

然后用不同的时间间隔输出了三行文本：

```js
async function asyncFunc() {
    await print(1000, "First");
    await print(4000, "Second");
    await print(3000, "Third");
}
asyncFunc();
```

这岂不是将异步操作变得像同步操作一样容易了吗！

这次的回答是肯定的，异步函数 async function 中可以使用 await 指令，**`await` 指令后必须跟着一个 Promise**，**异步函数会在这个 Promise 运行中暂停，直到其运行结束再继续运行**。

异步函数实际上原理与 Promise 原生 API 的机制是一模一样的，只不过更便于程序员阅读。

处理异常的机制将用 try-catch 块实现：

```js
async function asyncFunc() {
    try {
        await new Promise(function (resolve, reject) {
            throw "Some error"; // 或者 reject("Some error")
        });
    } catch (err) {
        console.log(err);
        // 会输出 Some error
    }
}
asyncFunc();
```

如果 Promise 有一个正常的返回值，await 语句也会返回它：

```js
async function asyncFunc() {
    let value = await new Promise(
        function (resolve, reject) {
            resolve("Return value");
        }
    );
    console.log(value);
}
asyncFunc();
```

程序会输出:

```
Return value
```



### JavaScript 代码规范

代码规范通常包括以下几个方面:

- 变量和函数的命名规则
- 空格，缩进，注释的使用规则。
- 其他常用规范……

#### 对象规则

对象定义的规则:

- 将左花括号与类名放在同一行。
- 冒号与属性值间有个空格。
- 字符串使用双引号，数字不需要。
- 最后一个属性-值对后面不要添加逗号。
- 将右花括号独立放在一行，并以分号作为结束符号。

```js
var person = {
  firstName: "John",
  lastName: "Doe",
  age: 50,
  eyeColor: "blue"
};
```

短的对象代码可以直接写成一行:

```js
var person = {firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"};
```

#### 每行代码字符小于 80

为了便于阅读每行字符建议小于数 80 个。

如果一个 JavaScript 语句超过了 80 个字符，建议在 运算符或者逗号后换行。

#### 使用小写文件名

大多 Web 服务器 (Apache, Unix) 对大小写敏感： london.jpg 不能通过 London.jpg 访问。

其他 Web 服务器 (Microsoft, IIS) 对大小写不敏感： london.jpg 可以通过 London.jpg 或 london.jpg 访问。

你必须保持统一的风格，我们建议统一使用小写的文件名。





## JS函数

### JavaScript函数定义

JavaScript使用function定义函数。

函数可以通过声明定义，也可以是一个表达式。

#### 函数声明

在之前的教程中，你已经了解了函数声明的语法 :

```js
function functionName(parameters) {
  执行的代码
}
```

函数声明后不会立即执行，会在我们需要的时候调用到。

#### 函数表达式

JavaScript 函数可以通过一个表达式定义。

函数表达式可以存储在变量中：

```js
var x = function (a, b) {return a * b};
```

在函数表达式存储在变量后，变量也可作为一个函数使用：

```js
var x = function (a, b) {return a * b};
var z = x(4, 3);
```

以上函数实际上是一个 **匿名函数** (函数没有名称)。

函数存储在变量中，不需要函数名称，通常通过变量名来调用。

#### Function() 构造函数

在以上实例中，我们了解到函数通过关键字 **function** 定义。

函数同样可以通过内置的 JavaScript 函数构造器（Function()）定义。

```js
var myFunction = new Function("a", "b", "return a * b");
var x = myFunction(4, 3);
```

实际上，你不必使用构造函数。上面实例可以写成：

```js
var myFunction = function (a, b) {return a * b};
var x = myFunction(4, 3);
```

> 在 JavaScript 中，很多时候，你需要避免使用 **new** 关键字。

#### 函数提升（Hoisting）

在之前的教程中我们已经了解了 "hoisting(提升)"。

提升（Hoisting）是 JavaScript 默认将当前作用域提升到前面去的行为。

提升（Hoisting）应用在变量的声明与函数的声明。

因此，函数可以在声明之前调用：

```js
myFunction(5);

function myFunction(y) {
    return y * y;
}
```

使用表达式定义函数时无法提升。

#### 函数可作为一个值使用

JavaScript 函数作为一个值使用：

```js
function myFunction(a, b) {
  return a * b;
}

var x = myFunction(4, 3);
```

JavaScript 函数可作为表达式使用：

```js
function myFunction(a, b) {
  return a * b;
}

var x = myFunction(4, 3) * 2;
```

#### 函数是对象

在 JavaScript 中使用 **typeof** 操作符判断函数类型将返回 "function" 。

但是JavaScript 函数描述为一个对象更加准确。

JavaScript 函数有 **属性** 和 **方法**。

arguments.length 属性返回函数调用过程接收到的参数个数：

```js
function myFunction(a, b) {
  return arguments.length;
}
```

#### 箭头函数

ES6 新增了箭头函数。

箭头函数表达式的语法比普通函数表达式更简洁。

```js
(参数1, 参数2, …, 参数N) => { 函数声明 }

(参数1, 参数2, …, 参数N) => 表达式(单一)
// 相当于：(参数1, 参数2, …, 参数N) =>{ return 表达式; }
```

当只有一个参数时，圆括号是可选的：

```js
(单一参数) => {函数声明}
单一参数 => {函数声明}
```

没有参数的函数应该写成一对圆括号:

```js
() => {函数声明}
```

```js
// ES5
var x = function(x, y) {
     return x * y;
}
 
// ES6
const x = (x, y) => x * y;
```

有的箭头函数都没有自己的 **this**。 不适合定义一个 **对象的方法**。

**当我们使用箭头函数的时候，箭头函数会默认帮我们绑定外层 this 的值**，所以在箭头函数中 this 的值和外层的 this 是一样的。

箭头函数是不能提升的，所以需要在使用之前定义。

**使用 `const` 比使用 `var` 更安全，因为函数表达式始终是一个常量。**

如果函数部分只是一个语句，则可以省略 return 关键字和大括号 {}，这样做是一个比较好的习惯:

```js
const x = (x, y) => { return x * y };
```



### JavaScript函数参数

JavaScript函数对参数的值没有进行任何的检查。

#### 函数显示参数（Parameters）与隐式参数（Arguments）

在先前的教程中，我们已经学习了函数的显示参数：

```js
functionName(parameter1, parameter2, parameter3) {
    // 要执行的代码……
}
```

函数显式参数在函数定义时列出。

函数隐式参数在函数调用时传递给函数真正的值。

#### 参数规则

JavaScript函数定义显示参数时没有指定数据类型。

JavaScript函数对隐式参数没有进行类型检测。

#### 默认参数

ES5 中如果函数在调用时未提供隐式参数，参数会默认设置为： **undefined**

有时这是可以接受的，但是建议最好为参数设置一个默认值：(ES5)

```js
function myFunction(x, y) {
    if (y === undefined) {
        y = 0;
    }
}
```

或者，更简单的方式：(ES5)

```js
function myFunction(x, y) {
    y = y || 0;
}
```

>  如果 y 已经定义，**y || 0** 返回 y，因为 y 是 true，否则返回 0，因为 undefined 为 false。

如果函数调用时设置了过多的参数，参数将无法被引用，因为无法找到对应的参数名。 只能使用 arguments 对象来调用。

#### ES6 函数可以自带参数

ES6 支持函数带有默认参数，就判断 undefined 和 || 的操作：

```js
function myFunction(x, y = 10) {
    // y is 10 if not passed or undefined    
    return x + y;
}
myFunction(0, 2) // 输出 2 
myFunction(5); // 输出 15, y 参数的默认值
```

#### arguments 对象

JavaScript 函数有个内置的对象 arguments 对象。

arguments 对象包含了函数调用的参数数组。

通过这种方式你可以很方便的找到最大的一个参数的值：

```js
x = findMax(1, 123, 500, 115, 44, 88);
 
function findMax() {
    var i, max = arguments[0];
    
    if(arguments.length < 2) return max;
 
    for (i = 0; i < arguments.length; i++) {
        if (arguments[i] > max) {
            max = arguments[i];
        }
    }
    return max;
}
```

或者创建一个函数用来统计所有数值的和：

```js
x = sumAll(1, 123, 500, 115, 44, 88);
 
function sumAll() {
    var i, sum = 0;
    for (i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
}
```

#### 通过值传递参数

在函数中调用的参数是函数的隐式参数。

JavaScript 隐式参数通过值来传递：函数仅仅只是获取值。

如果函数修改参数的值，不会修改显式参数的初始值（在函数外定义）。

隐式参数的改变在函数外是不可见的。

#### 通过对象传递参数

在JavaScript中，可以引用对象的值。

因此我们在函数内部修改对象的属性就会修改其初始的值。

修改对象属性可作用于函数外部（全局变量）。

修改对象属性在函数外是可见的。



### JavaScript 函数调用

JavaScript 函数有 4 种调用方式。

每种方式的不同在于 **this** 的初始化。

#### this 关键字

一般而言，在Javascript中，this指向函数执行时的当前对象。

> 注意 **this** 是保留关键字，你不能修改 **this** 的值。

#### 调用 JavaScript 函数

在之前的章节中我们已经学会了如何创建函数。

函数中的代码在函数被调用后执行。

#### 作为一个函数执行

```js
function myFunction(a, b) {
    return a * b;
}
myFunction(10, 2);           // myFunction(10, 2) 返回 20
```

以上函数不属于任何对象。但是在 JavaScript 中它始终是默认的全局对象。

在 HTML 中默认的全局对象是 HTML 页面本身，所以函数是属于 HTML 页面。

在浏览器中的页面对象是浏览器窗口(window 对象)。以上函数会自动变为 window 对象的函数。

myFunction() 和 window.myFunction() 是一样的：

```js
function myFunction(a, b) {
    return a * b;
}
window.myFunction(10, 2);    // window.myFunction(10, 2) 返回 20
```

>  这是调用 JavaScript 函数常用的方法， 但不是良好的编程习惯 全局变量，方法或函数容易造成命名冲突的bug。

#### 全局对象

当函数没有被自身的对象调用时 **this** 的值就会变成全局对象。

在 web 浏览器中全局对象是浏览器窗口（window 对象）。

该实例返回 **this** 的值是 window 对象:

```js
function myFunction() {
    return this;
}
myFunction();                // 返回 window 对象
```

>  函数作为全局对象调用，会使 **this** 的值成为全局对象。 使用 window 对象作为一个变量容易造成程序崩溃。

#### 函数作为方法调用

在 JavaScript 中你可以将函数定义为对象的方法。

以下实例创建了一个对象 (**myObject**), 对象有两个属性 (**firstName** 和 **lastName**), 及一个方法 (**fullName**):

```js
var myObject = {
    firstName:"John",
    lastName: "Doe",
    fullName: function () {
        return this.firstName + " " + this.lastName;
    }
}
myObject.fullName();         // 返回 "John Doe"
```

**fullName** 方法是一个函数。函数属于对象。 **myObject** 是函数的所有者。

**this**对象，拥有 JavaScript 代码。实例中 **this** 的值为 **myObject** 对象。

测试以下！修改 **fullName** 方法并返回 **this** 值:

```js
var myObject = {
    firstName:"John",
    lastName: "Doe",
    fullName: function () {
        return this;
    }
}
myObject.fullName();          // 返回 [object Object] (所有者对象)
```

> 函数作为对象方法调用，会使得 **this** 的值成为对象本身。

#### 使用构造函数调用函数

如果函数调用前使用了 **new** 关键字, 则是调用了构造函数。

这看起来就像创建了新的函数，但实际上 JavaScript 函数是重新创建的对象：

```js
// 构造函数:
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}
 
// This creates a new object
var x = new myFunction("John","Doe");
x.firstName;                             // 返回 "John"
```

构造函数的调用会创建一个新的对象。新对象会继承构造函数的属性和方法。

> 构造函数中 **this** 关键字没有任何的值。
> **this** 的值在函数调用实例化对象(new object)时创建。

#### 作为函数方法调用函数

在 JavaScript 中, 函数是对象。JavaScript 函数有它的属性和方法。

**`call()`** 和 **`apply()`** 是预定义的函数方法。 两个方法可用于调用函数，**两个方法的第一个参数必须是对象本身**。

```js
function myFunction(a, b) {
    return a * b;
}
myObject = myFunction.call(myObject, 10, 2);     // 返回 20
```

```js
function myFunction(a, b) {
    return a * b;
}
myArray = [10, 2];
myObject = myFunction.apply(myObject, myArray);  // 返回 20
```

两个方法都使用了对象本身作为第一个参数。 两者的区别在于第二个参数： **apply**传入的是一个**参数数组**，也就是**将多个参数组合成为一个数组传入**，而**call**则**作为call的参数传入**（从第二个参数开始）。

在 JavaScript 严格模式(strict mode)下, 在调用函数时第一个参数会成为 **this** 的值， 即使该参数不是一个对象。

在 JavaScript 非严格模式(non-strict mode)下, 如果第一个参数的值是 null 或 undefined, 它将使用全局对象替代。

> 通过 call() 或 apply() 方法你可以设置 **this** 的值, 且作为已存在对象的新方法调用。(例见本文档**JavaScript this 关键字->显示函数绑定**)



### JavaScript闭包

JavaScript 变量可以是局部变量或全局变量。

私有变量可以用到闭包。

#### 全局变量

函数可以访问由函数内部定义的变量，如：

```js
function myFunction() {
    var a = 4;
    return a * a;
}
```

函数也可以访问函数外部定义的变量，如：

```js
var a = 4;
function myFunction() {
    return a * a;
}
```

后面一个实例中， **a** 是一个 **全局** 变量。

在web页面中全局变量属于 window 对象。

全局变量可应用于页面上的所有脚本。

在第一个实例中， **a** 是一个 **局部** 变量。

局部变量只能用于定义它函数内部。对于其他的函数或脚本代码是不可用的。

全局和局部变量即便名称相同，它们也是两个不同的变量。修改其中一个，不会影响另一个的值。

> 变量声明时如果不使用 **var** 关键字，那么它就是一个全局变量，即便它在函数内定义。

#### 变量生命周期

全局变量的作用域是全局性的，即在整个JavaScript程序中，全局变量处处都在。

而在函数内部声明的变量，只在函数内部起作用。这些变量是局部变量，作用域是局部性的；函数的参数也是局部性的，只在函数内部起作用。

#### 计数器困境

设想下如果你想统计一些数值，且该计数器在所有函数中都是可用的。

你可以使用全局变量，函数设置计数器递增：

```js
var counter = 0;
 
function add() {
   return counter += 1;
}
 
add();
add();
add();
 
// 计数器现在为 3
```

计数器数值在执行 add() 函数时发生变化。

但问题来了，页面上的任何脚本都能改变计数器，即便没有调用 add() 函数。

如果我在函数内声明计数器，如果没有调用函数将无法修改计数器的值：

```js
function add() {
    var counter = 0;
    return counter += 1;
}
 
add();
add();
add();
 
// 本意是想输出 3, 但事与愿违，输出的都是 1 !
```

以上代码将无法正确输出，每次我调用 add() 函数，计数器都会设置为 1。

**JavaScript 内嵌函数可以解决该问题。**

#### JavaScript 内嵌函数

所有函数都能访问全局变量。 

实际上，在 JavaScript 中，所有函数都能访问它们上一层的作用域。

JavaScript 支持嵌套函数。嵌套函数可以访问上一层的函数变量。

该实例中，内嵌函数 **plus()** 可以访问父函数的 **counter** 变量：

```js
function add() {
    var counter = 0;
    function plus() {counter += 1;}
    plus();    
    return counter; 
}
```

如果我们能在外部访问 **plus()** 函数，这样就能解决计数器的困境。

我们同样需要确保 **counter = 0** 只执行一次。

**我们需要闭包。**



#### JavaScript 闭包

还记得函数自我调用吗？该函数会做什么？

```js
var add = (function () {
    var counter = 0;
    return function () {return counter += 1;}
})();
 
add();
add();
add();
 
// 计数器为 3
```

变量 **add** 指定了函数自我调用的返回字值。

自我调用函数只执行一次。设置计数器为 0。并返回函数表达式。

add变量可以作为一个函数使用。非常棒的部分是它可以访问函数上一层作用域的计数器。

这个叫作 JavaScript **闭包。**它使得函数拥有私有变量变成可能。

计数器受匿名函数的作用域保护，只能通过 add 方法修改。

>  闭包是一种保护私有变量的机制，在函数执行时形成私有的作用域，保护里面的私有变量不受外界干扰。直观的说就是形成一个不销毁的栈环境。



## JS类

### JavaScript类

**类是用于创建对象的模板。**

我们使用 class 关键字来创建一个类，类体在一对大括号 **{}** 中，我们可以在大括号 **{}** 中定义类成员的位置，如方法或构造函数。

每个类中包含了一个特殊的方法 **constructor()**，它是类的构造函数，这种方法用于创建和初始化一个由 **class** 创建的对象。

创建一个类的语法格式如下：

```js
class ClassName {
  constructor() { ... }
}
```

实例：

```js
class Runoob {
  constructor(name, url) {
    this.name = name;
    this.url = url;
  }
}
```

以上实例创建了一个类，名为 "Runoob"。

类中初始化了两个属性： "name" 和 "url"。

#### 使用类

定义好类后，我们就可以使用 **new** 关键字来创建对象：

```js
class Runoob {
  constructor(name, url) {
    this.name = name;
    this.url = url;
  }
}
 
let site = new Runoob("菜鸟教程",  "https://www.runoob.com");
```

创建对象时会自动调用构造函数方法 **constructor()**。

#### 类表达式

类表达式是定义类的另一种方法。类表达式可以命名或不命名。命名类表达式的名称是该类体的局部名称

```js
// 未命名/匿名类
let Runoob = class {
  constructor(age, url) {
    this.age = age;
    this.url = url;
  }
};
console.log(Runoob.name);
// output: "Runoob"
 
// 命名类
let Runoob = class Runoob2 {
  constructor(age, url) {
    this.age = age;
    this.url = url;
  }
};
console.log(Runoob.name);
// 输出: "Runoob2"
```

构造方法

构造方法是一种特殊的方法：

- 构造方法名为 constructor()。
- 构造方法在创建新对象时会自动执行。
- 构造方法用于初始化对象属性。
- 如果不定义构造方法，JavaScript 会自动添加一个空的构造方法。

#### 类的方法

我们使用关键字 **class** 创建一个类，可以添加一个 **constructor()** 方法，然后添加任意数量的方法。

```js
class ClassName {
  constructor() { ... }
  method_1() { ... }
  method_2() { ... }
  method_3() { ... }
}
```

以下实例我们创建一个 "age" 方法，用于返回网站年龄：

```js
class Runoob {
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
  age() {
    let date = new Date();
    return date.getFullYear() - this.year;
  }
}
 
let runoob = new Runoob("菜鸟教程", 2018);
document.getElementById("demo").innerHTML =
"菜鸟教程 " + runoob.age() + " 岁了。";
```

我们还可以向类的方法发送参数：

```js
class Runoob {
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
  age(x) {
    return x - this.year;
  }
}
 
let date = new Date();
let year = date.getFullYear();
 
let runoob = new Runoob("菜鸟教程", 2020);
document.getElementById("demo").innerHTML=
"菜鸟教程 " + runoob.age(year) + " 岁了。";
```

#### 严格模式 "use strict"

类声明和类表达式的主体都执行在严格模式下。比如，构造函数，静态方法，原型方法，getter 和 setter 都在严格模式下执行。

如果你没有遵循严格模式，则会出现错误：

```js
class Runoob {
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
  age() {
    // date = new Date();  // 错误，不能使用未声明的变量
    let date = new Date(); // 正确
    return date.getFullYear() - this.year;
  }
}
```

#### 参考

类方法

| 方法                                                         | 描述                         |
| :----------------------------------------------------------- | :--------------------------- |
| [constructor()](https://www.runoob.com/js/jsref-constructor-class.html) | 构造函数，用于创建和初始化类 |

类关键字

| 关键字                                                       | 描述                   |
| :----------------------------------------------------------- | :--------------------- |
| [extends](https://www.runoob.com/js/jsref-class-extends.html) | 继承一个类             |
| [static](https://www.runoob.com/js/jsref-class-static.html)  | 在类中定义一个静态方法 |
| [super](https://www.runoob.com/js/jsref-class-super.html)    | 调用父类的构造方法     |





### JavaScript类继承

JavaScript 并没有像其他编程语言一样具有传统的类，而是基于原型的继承模型。

ES6 引入了类和 **class** 关键字，但底层机制仍然基于原型继承。

#### 使用原型链继承

在下面实例中，Animal 是一个基类，Dog 是一个继承自 Animal 的子类，Dog.prototype 使用 Object.create(Animal.prototype) 来创建一个新对象，它继承了 Animal.prototype 的方法和属性，通过将 Dog.prototype.constructor 设置为 Dog，确保继承链上的构造函数正确。

```js
function Animal(name) {
  this.name = name;
}
 
Animal.prototype.eat = function() {
  console.log(this.name + " is eating.");
};
 
function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
 
// 建立原型链，让 Dog 继承 Animal
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;
 
Dog.prototype.bark = function() {
  console.log(this.name + " is barking.");
};
 
var dog = new Dog("Buddy", "Labrador");
dog.eat();  // 调用从 Animal 继承的方法
dog.bark(); // 调用 Dog 的方法
```

#### 使用 ES6 类继承

ES6 引入了 class 关键字，使得定义类和继承更加清晰，extends 关键字用于建立继承关系，super 关键字用于在子类构造函数中调用父类的构造函数。

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
 
  eat() {
    console.log(this.name + " is eating.");
  }
}
 
class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
 
  bark() {
    console.log(this.name + " is barking.");
  }
}
 
const dog = new Dog("Buddy", "Labrador");
dog.eat();
dog.bark();
```

不论是使用原型链继承还是 ES6 类继承，都可以实现类似的继承效果，在选择哪种方法时，可以根据个人偏好和项目需求来决定。



#### getter 和 setter

类中我们可以使用 getter 和 setter 来获取和设置值，getter 和 setter 都需要在严格模式下执行。

getter 和 setter 可以使得我们对属性的操作变的很灵活。

类中添加 getter 和 setter 使用的是 get 和 set 关键字。

以下实例为 sitename 属性创建 getter 和 setter：

```js
class Runoob {
  constructor(name) {
    this.sitename = name;
  }
  get s_name() {
    return this.sitename;
  }
  set s_name(x) {
    this.sitename = x;
  }
}
 
let noob = new Runoob("菜鸟教程");
 
document.getElementById("demo").innerHTML = noob.s_name;
```

**注意：**即使 getter 是一个方法，当你想获取属性值时也不要使用括号。

getter/setter 方法的名称不能与属性的名称相同，在本例中属名为 sitename。

很多开发者在属性名称前使用下划线字符 **_** 将 getter/setter 与实际属性分开：

以下实例使用下划线 **_** 来设置属性，并创建对应的 getter/setter 方法：

```js
class Runoob {
  constructor(name) {
    this._sitename = name;
  }
  get sitename() {
    return this._sitename;
  }
  set sitename(x) {
    this._sitename = x;
  }
}
 
let noob = new Runoob("菜鸟教程");
 
document.getElementById("demo").innerHTML = noob.sitename;
```

要使用 setter，请使用与设置属性值时相同的语法，虽然 set 是一个方法，但需要不带**括号**：

```js
class Runoob {
  constructor(name) {
    this._sitename = name;
  }
  set sitename(x) {
    this._sitename = x;
  }
  get sitename() {
    return this._sitename;
  }
}
 
let noob = new Runoob("菜鸟教程");
noob.sitename = "RUNOOB";
document.getElementById("demo").innerHTML = noob.sitename;
```



#### 提升

函数声明和类声明之间的一个重要区别在于, 函数声明会提升，类声明不会。

你首先需要声明你的类，然后再访问它，否则类似以下的代码将抛出 ReferenceError：

```js
// 这里不能这样使用类，因为还没有声明
// noob = new Runoob("菜鸟教程")
// 报错
 
class Runoob {
  constructor(name) {
    this.sitename = name;
  }
}
 
// 这里可以使用类了
let noob = new Runoob("菜鸟教程")
```



### JavaScript静态方法

静态方法是使用 static 关键字修饰的方法，又叫类方法，属于类的，但不属于对象，在实例化对象之前可以通过 **类名.方法名** 调用静态方法。

静态方法不能在对象上调用，只能在类中调用。

```js
class Runoob {
  constructor(name) {
    this.name = name;
  }
  static hello() {
    return "Hello!!";
  }
}
 
let noob = new Runoob("菜鸟教程");
 
// 可以在类中调用 'hello()' 方法
document.getElementById("demo").innerHTML = Runoob.hello();
 
// 不能通过实例化后的对象调用静态方法
// document.getElementById("demo").innerHTML = noob.hello();
// 以上代码会报错
```

如果你想在对象 noob 中使用静态方法，可以作为一个参数传递给它：

```js
class Runoob {
  constructor(name) {
    this.name = name;
  }
  static hello(x) {
    return "Hello " + x.name;
  }
}
let noob = new Runoob("菜鸟教程");
document.getElementById("demo").innerHTML = Runoob.hello(noob);
```



## JS高级教程

### JavaScript对象

#### 创建 JavaScript 对象

通过 JavaScript，您能够定义并创建自己的对象。

创建新对象有两种不同的方法：

- 使用 Object 定义并创建对象的实例。
- 使用函数来定义对象，然后创建新的对象实例。

#### 使用 Object

在 JavaScript 中，几乎所有的对象都是 Object 类型的实例，它们都会从 Object.prototype 继承属性和方法。

Object 构造函数创建一个对象包装器。

Object 构造函数，会根据给定的参数创建对象，具体有以下情况：

- 如果给定值是 null 或 undefined，将会创建并返回一个空对象。
- 如果传进去的是一个基本类型的值，则会构造其包装类型的对象。
- 如果传进去的是引用类型的值，仍然会返回这个值，经他们复制的变量保有和源对象相同的引用地址。
- 当以非构造函数形式被调用时，Object 的行为等同于 new Object()。

语法格式：

```js
// 以构造函数形式来调用
new Object([value])
```

value 可以是任何值。

以下实例使用 Object 生成布尔对象：

```js
// 等价于 o = new Boolean(true);
var o = new Object(true);
```

这个例子创建了对象的一个新实例，并向其添加了四个属性：

```js
person=new Object(); 
person.firstname="John"; person.lastname="Doe"; 
person.age=50;
person.eyecolor="blue";
```

也可以使用对象字面量来创建对象，语法格式如下：

```js
var myObject = {
    key1: value1,
    key2: value2,
    // 更多键值对...
};
```

- **myObject:** 变量名，用于引用整个对象。
- **key1, key2:** 属性名称，可以是字符串或标识符。
- **value1, value2:** 属性的值，可以是任意 JavaScript 数据类型，包括数字、字符串、布尔值、函数、数组、甚至其他对象。

```js
var person = {
    firstName: 'John',
    lastName: 'Doe',
    age: 30,
    isStudent: false,
    greet: function() {
        console.log('Hello, I am ' + this.firstName + ' ' + this.lastName);
    }
};

console.log(person.firstName);  // 输出: John
person.greet();  // 输出: Hello, I am John Doe
```

```js
person={firstname:"John",lastname:"Doe",age:50,eyecolor:"blue"};
```

JavaScript 对象就是一个 **name:value** 集合。

#### 使用对象构造器

本例使用函数来构造对象：

```js
function person(firstname,lastname,age,eyecolor) {
    this.firstname=firstname;
    this.lastname=lastname;
    this.age=age;
    this.eyecolor=eyecolor;
}
```

在JavaScript中，this通常指向的是我们正在执行的函数本身，或者是指向该函数所属的对象（运行时）



### JavaScript prototype（原型对象）

所有的JavaScript对象都会从一个 prototype（原型对象）中继承属性和方法。

在前面的章节中我们学会了如何使用对象的构造器（constructor）：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
var myFather = new Person("John", "Doe", 50, "blue");
var myMother = new Person("Sally", "Rally", 48, "green");
```

我们也知道**在一个已存在构造器的对象中是不能添加新的属性**：

```js
Person.nationality = "English";
```

要添加一个新的属性需要在在构造器函数中添加：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
  this.nationality = "English";
}
```



#### prototype 继承

所有的 JavaScript 对象都会从一个 prototype（原型对象）中继承属性和方法：

- `Date` 对象从 `Date.prototype` 继承。
- `Array` 对象从 `Array.prototype` 继承。
- `Person` 对象从 `Person.prototype` 继承。

所有 JavaScript 中的对象都是位于原型链顶端的 Object 的实例。

JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

`Date` 对象, `Array` 对象, 以及 `Person` 对象从 `Object.prototype` 继承。

#### 添加属性和方法

有的时候我们想要在所有已经存在的对象添加新的属性或方法。

另外，有时候我们想要在对象的构造函数中添加属性或方法。

**使用 prototype 属性就可以给对象的构造函数添加新的属性**：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
Person.prototype.nationality = "English";
```

当然我们也可以使用 prototype 属性就可以给对象的构造函数添加新的方法：

```js
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}
 
Person.prototype.name = function() {
  return this.firstName + " " + this.lastName;
};
```

