### TypeScript基础类型

[菜鸟教程](https://www.runoob.com/typescript/ts-tutorial.html)

#### 数据类型

##### 任意类型：any 

声明为any的变量可以赋予任意类型的值

##### 数字类型：number

双精度64为浮点值。可以用来表示整数和分数。

##### 字符串类型：string

一个字符串系列，使用单引号（**`'`**）或双引号（**`"`**）来表示字符串类型。反引号（**`` `**）来定义多行文本和内嵌表达式。

```ts
let name: string = "Runoob";
let years: number = 5;
let words: string = `您好，今年是 ${ name } 发布 ${ years + 1} 周年`;
```

##### 布尔类型：boolean

表示逻辑值：true和false。

```ts
let flag: boolean = true;
```

##### 数组类型

声明变量为数组。

```ts
// 在元素类型后面加上[]
let arr: number[] = [1,2];

// 或者使用数组泛型
let arr:Array<number> = [1,2];
```

##### 元组

元组类型用来表示一致元素数量和类型的数组，各元素的类型不必相同，对应位置的类型需要相同。

```ts
let x: [string, number];
x = ['Runoob', 1];		// 运行正常
x = [1,'Runoob'];		// 报错
console.log(x[0]);		// 输出Runoob
```

##### 枚举：enum

枚举类型用于定义数值集合。

```ts
enum Color {Red, Green, Blue};
let c: Color = Color.Blue;
console.log(c);		// 输出 2
```

##### void

用于标识方法返回值的类型，表示该方法没有返回值。

```ts
function hello(): void {
    alert("Hello Runoob");
}
```

##### null

表示**对象值**缺失。

##### undefined

用于初始化变量为一个未定义的值。

##### never

never是其他类型（包括null和undefined）的子类型，代表从不会出现的值。

> **注意：**TypeScript 和 JavaScript 没有整数类型。



#### Any类型的使用

任意值是TypeScript针对编程时类型不明确的变量使用的一种数据类型，它常用于以下三种情况。

1、变量的值会动态改变，比如来自用户的输入，任意值类型可以让这些变量跳过编译阶段的类型检查，示例代码如下：

```ts
let x: any = 1;    // 数字类型
x = 'I am who I am';    // 字符串类型
x = false;    // 布尔类型
```

2、改写现有代码时，任意值允许在编译时可选择地包含或移除类型检查，示例代码如下：

```ts
let x: any = 4;
x.ifItExists();    // 正确，ifItExists方法在运行时可能存在，但这里并不会检查
x.toFixed();    // 正确
```

3、定义存储各种类型数据的数组时，示例代码如下：

```ts
let arrayList: any[] = [1, false, 'fine'];
arrayList[1] = 100;
```



#### Null和Undefined的使用

**null**：在 JavaScript 中 null 表示 "什么都没有"。null是一个只有一个值的特殊类型。表示一个空对象引用。用 typeof 检测 null 返回是 object。

**undefined**：在 JavaScript 中, undefined 是一个没有设置值的变量。typeof 一个没有值的变量会返回 undefined。

Null 和 Undefined 是其他任何类型（包括 void）的子类型，可以赋值给其它类型，如数字类型，此时，赋值后的类型会变成 null 或 undefined。而**在TypeScript中启用严格的空校验（--strictNullChecks）特性**，**就可以使得null 和 undefined 只能被赋值给 void 或本身对应的类型**，示例代码如下：

```ts
// 启用 --strictNullChecks
let x: number;
x = 1; // 编译正确
x = undefined;    // 编译错误
x = null;    // 编译错误
```

上面的例子中变量 x 只能是数字类型。如果一个类型可能出现 null 或 undefined， 可以用 | 来支持多种类型，示例代码如下：

```ts
// 启用 --strictNullChecks
let x: number | null | undefined;
x = 1; // 编译正确
x = undefined;    // 编译正确
x = null;    // 编译正确
```

#### never类型的使用

never 是其它类型（包括 null 和 undefined）的子类型，代表从不会出现的值。这意味着声明为 never 类型的变量只能被 never 类型所赋值，在函数中它通常表现为**抛出异常或无法执行到终止点（例如无限循环）**，示例代码如下：

```ts
let x: never;
let y: number;

// 编译错误，数字类型不能转为 never 类型
x = 123;

// 运行正确，never 类型可以赋值给 never类型
x = (()=>{ throw new Error('exception')})();

// 运行正确，never 类型可以赋值给 数字类型
y = (()=>{ throw new Error('exception')})();

// 返回值为 never 的函数可以是抛出异常的情况
function error(message: string): never {
    throw new Error(message);
}

// 返回值为 never 的函数可以是无法被执行到的终止点的情况
function loop(): never {
    while (true) {}
}
```



### TypeScript变量声明

变量是一种使用方便的占位符，用于引用计算机内存地址。

我们可以吧变量看做存储数据的容器。

TypeScript变量的命名规则：

+ 变量名称可以包含数字和字母。
+ 除了下划线**`_`**和美元**`$`**符号外，不能包含其他特殊字符，包括空格。
+ 变量名不能以数字开头。

变量使用前必须先声明，我们可以使用var来声明变量。

我们可以一下四种方式来声明变量：

**1、声明变量的类型及初始值**

```ts
var [变量名] : [类型] = 值;
```

例如：

```ts
var uname:string = "Runoob";
```

**2、声明变量的类型，但没有初始值，变量值会设置为 undefined：**

```ts
var [变量名] : [类型];
```

例如：

```ts
var uname:string;
```

**3、声明变量并初始值，但不设置类型，该变量可以是任意类型：**

```ts
var [变量名] = 值;
```

例如：

```ts
var uname = "Runoob";
```

**4、声明变量没有设置类型和初始值，类型可以是任意类型，默认初始值为 undefined：**

```ts
var [变量名];
```

例如：

```ts
var uname;
```



TypeScript 遵循强类型，如果将不同的类型赋值给变量会编译错误，如下实例：

```ts
var num:number = "hello"     // 这个代码会编译错误
```



#### 类型断言（Type Assertion）

类型断言可以用来手动指定一个值的类型，即允许变量从一种类型更改为另一个类型。

语法格式：

```ts
<类型> 值
```

或

```ts
值 as 类型
```

实例

```ts
var str = '1' 
var str2:number = <number> <any> str   //str、str2 是 string 类型
console.log(str2)
```

#### TypeScript 是怎么确定单个断言是否足够

当 S 类型是 T 类型的子集，或者 T 类型是 S 类型的子集时，S 能被成功断言成 T。这是为了在进行类型断言时提供额外的安全性，完全毫无根据的断言是危险的，如果你想这么做，你可以使用 any。

它之所以不被称为**类型转换**，是因为转换通常意味着某种运行时的支持。但是，类型断言纯粹是一个编译时语法，同时，它也是一种为编译器提供关于如何分析代码的方法。

编译后，以上代码会生成如下 JavaScript 代码：

```ts
var str = '1';
var str2 = str;  //str、str2 是 string 类型
console.log(str2);
```

执行输出结果为：

```
1
```

#### 类型推断

当类型没有给出时，TypeScript 编译器利用类型推断来推断类型。

如果由于缺乏声明而不能推断出类型，那么它的类型被视作默认的动态 any 类型。

```ts
var num = 2;    // 类型推断为 number
console.log("num 变量的值为 "+num);
num = "12";    // 编译错误
console.log(num);
```

- 第一行代码声明了变量 num 并=设置初始值为 2。 注意变量声明没有指定类型。因此，程序使用类型推断来确定变量的数据类型，第一次赋值为 2，**num** 设置为 number 类型。

- 第三行代码，当我们再次为变量设置字符串类型的值时，**这时编译会错误。因为变量已经设置为了 number 类型。**

  ```
  error TS2322: Type '"12"' is not assignable to type 'number'.
  ```

#### 变量作用域

变量作用域指定了变量定义的位置。

程序中变量的可用性由变量作用域决定。

TypeScript 有以下几种作用域：

- **全局作用域** − 全局变量定义在程序结构的外部，它可以在你代码的任何位置使用。
- **类作用域** − 这个变量也可以称为 **字段**。类变量声明在一个类里头，但在类的方法外面。 该变量可以通过类的对象来访问。类变量也可以是静态的，静态的变量可以通过类名直接访问。
- **局部作用域** − 局部变量，局部变量只能在声明它的一个代码块（如：方法）中使用。

以下实例说明了三种作用域的使用：

```ts
var global_num = 12          // 全局变量
class Numbers { 
   num_val = 13;             // 实例变量
   static sval = 10;         // 静态变量
   
   storeNum():void { 
      var local_num = 14;    // 局部变量
   } 
} 
console.log("全局变量为: "+global_num)  
console.log(Numbers.sval)   // 静态变量
var obj = new Numbers(); 
console.log("实例变量: "+obj.num_val)
```

以上代码使用 tsc 命令编译为 JavaScript 代码为：

```js
var global_num = 12; // 全局变量
var Numbers = /** @class */ (function () {
    function Numbers() {
        this.num_val = 13; // 实例变量
    }
    Numbers.prototype.storeNum = function () {
        var local_num = 14; // 局部变量
    };
    Numbers.sval = 10; // 静态变量
    return Numbers;
}());
console.log("全局变量为: " + global_num);
console.log(Numbers.sval); // 静态变量
var obj = new Numbers();
console.log("实例变量: " + obj.num_val);
```

执行以上 JavaScript 代码，输出结果为：

```
全局变量为: 12
10
实例变量: 13
```

如果我们在方法外部调用局部变量 local_num，会报错：

```
error TS2322: Could not find symbol 'local_num'.
```



### TypeScript循环

#### for…of 、forEach、every 和 some 循环

此外，TypeScript 还支持 for…of 、forEach、every 和 some 循环。

for...of 语句创建一个循环来迭代可迭代的对象。在 ES6 中引入的 for...of 循环，以替代 for...in 和 forEach() ，并支持新的迭代协议。for...of 允许你遍历 Arrays（数组）, Strings（字符串）, Maps（映射）, Sets（集合）等可迭代的数据结构等。

```ts
let someArray = [1, "string", false];
 
for (let entry of someArray) {
    console.log(entry); // 1, "string", false
}
```

forEach、every 和 some 是 JavaScript 的循环语法，TypeScript 作为 JavaScript 的语法超集，当然默认也是支持的。

因为 forEach 在 iteration 中是无法返回的，所以可以使用 every 和 some 来取代 forEach。

```ts
let list = [4, 5, 6];
list.forEach((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
});
```

```ts
// TypeScript every 循环
let list = [4, 5, 6];
list.every((val, idx, array) => {
    // val: 当前值
    // idx：当前index
    // array: Array
    return true; // Continues
    // Return false will quit the iteration
});
```



### TypeScript函数

函数是一组一起执行一个任务的语句。

您可以把代码划分到不同的函数中。如何划分代码到不同的函数中是由您来决定的，但在逻辑上，划分通常是根据每个函数执行一个特定的任务来进行的。

函数声明告诉编译器函数的名称、返回类型和参数。函数定义了函数的实际主体。

#### 函数定义

函数就是包裹在花括号中的代码块，前面使用了关键词function：

```ts
function function_name()
{
    // 执行代码
}
```

实例

```ts
function () {   
    // 函数定义
    console.log("调用函数") 
}
```

#### 调用函数

函数只有通过调用才可以执行函数内的代码。

语法格式如下所示：

```ts
function_name()
```

实例

```ts
function test() {   // 函数定义
    console.log("调用函数") 
} 
test()              // 调用函数
```

#### 函数返回值

有时，我们会希望函数将执行的结果返回到调用它的地方。

通过使用return语句就可以实现。

在使用return语句时，函数就会停止执行，并返回指定的值。

语法格式如下所示：

```ts
function function_name():return_type { 
    // 语句
    return value; 
}
```

- return_type 是返回值的类型。
- return 关键词后跟着要返回的结果。
- 一般情况下，一个函数只有一个 return 语句。
- 返回值的类型需要与函数定义的返回类型(return_type)一致。

```ts
// 函数定义
function greet():string { // 返回一个字符串
    return "Hello World" 
} 
 
function caller() { 
    var msg = greet() // 调用 greet() 函数 
    console.log(msg) 
} 
 
// 调用函数
caller()
```

- 实例中定义了函数 *greet()*，返回值的类型为 string。
- *greet()* 函数通过 return 语句返回给调用它的地方，即变量 msg，之后输出该返回值。

编译以上代码，得到以下 JavaScript 代码：

```ts
// 函数定义
function greet() {
    return "Hello World";
}
function caller() {
    var msg = greet(); // 调用 greet() 函数 
    console.log(msg);
}
// 调用函数
caller();
```

#### 带参数函数

在调用函数时，您可以向其传递值，这些值被称为参数。

这些参数可以在函数中使用。

您可以向函数发送多个参数，每个参数使用逗号 **,** 分隔：

语法格式如下所示：

```ts
function func_name( param1 [:datatype], param2 [:datatype]) {   
}
```

- param1、param2 为参数名。
- datatype 为参数类型。

实例

```ts
function add(x: number, y: number): number {
    return x + y;
}
console.log(add(1,2))
```

- 实例中定义了函数 *add()*，返回值的类型为 number。
- *add()* 函数中定义了两个 number 类型的参数，函数内将两个参数相加并返回。

```ts
function add(x, y) {
    return x + y;
}
console.log(add(1, 2));
```

#### 可选参数和默认参数

##### 可选参数

在 TypeScript 函数里，如果我们定义了参数，则我们必须传入这些参数，除非将这些参数设置为可选，可选参数使用问号标识 `？`。

实例

```ts
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}
 
let result1 = buildName("Bob");                  // 错误，缺少参数
let result2 = buildName("Bob", "Adams", "Sr.");  // 错误，参数太多了
let result3 = buildName("Bob", "Adams");         // 正确
```

以下实例，我们将 lastName 设置为可选参数：

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}
 
let result1 = buildName("Bob");  // 正确
let result2 = buildName("Bob", "Adams", "Sr.");  // 错误，参数太多了
let result3 = buildName("Bob", "Adams");  // 正确
```

可选参数必须跟在必需参数后面。 如果上例我们想让 firstName 是可选的，lastName 必选，那么就要调整它们的位置，把 firstName 放在后面。

如果都是可选参数就没关系。

#### 默认参数

我们也可以设置参数的默认值，这样在调用函数的时候，如果不传入该参数的值，则使用默认参数，语法格式为：

```ts
function function_name(param1[:type],param2[:type] = default_value) { 
}
```

注意：参数不能同时设置为可选和默认。

实例

以下实例函数的参数 rate 设置了默认值为 0.50，调用该函数时如果未传入参数则使用该默认值：

```ts
function calculate_discount(price:number,rate:number = 0.50) {
    var discount = price * rate;    
    console.log("计算结果: ",discount);
}
calculate_discount(1000)
calculate_discount(1000,0.30)
```

编译以上代码，得到以下 JavaScript 代码：

```js
function calculate_discount(price, rate) {
    if (rate === void 0) {
        rate = 0.50;
    }
    var discount = price * rate;    
    console.log("计算结果: ", discount);
}
calculate_discount(1000); 
calculate_discount(1000, 0.30);
```

输出结果为：

```
计算结果:  500
计算结果:  300
```

#### 剩余参数

有一种情况，我们不知道要向函数传入多少个参数，这时候我们就可以使用剩余参数来定义。

剩余参数语法允许我们将一个不确定数量的参数作为一个数组传入。

```ts
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

函数的最后一个命名参数 restOfName 以 ... 为前缀，它将成为一个由剩余参数组成的数组，索引值从0（包括）到 restOfName.length（不包括）。

```js
function addNumbers(...nums:number[]) {
    var i;
    var sum:number = 0;
    for(i = 0;i<nums.length;i++) {
        sum = sum + nums[i];
    }
    console.log("和为：",sum)
}
addNumbers(1,2,3)
addNumbers(10,10,10,10,10)
```

编译以上代码，得到以下 JavaScript 代码：

```js
function addNumbers() {
    var nums = [];
    for (var _i = 0; _i < arguments.length; _i++) {
        nums[_i] = arguments[_i];
    }
    var i;
    var sum = 0;
    for (i = 0; i < nums.length; i++) {
        sum = sum + nums[i];
    }
    console.log("和为：", sum);
}
addNumbers(1, 2, 3);
addNumbers(10, 10, 10, 10, 10);
```

输出结果为：

```
和为： 6
和为： 50
```

#### 匿名函数

匿名函数是一个没有函数名的函数。

匿名函数在程序运行时动态声明，除了没有函数名外，其他的与标准函数一样。

我们可以将匿名函数赋值给一个变量，这种表达式就成为函数表达式。

语法格式如下：

```ts
var res = function( [arguments] ) { ... }
```

不带参数匿名函数：

```ts
var msg = function() {
    return "hello world";
}
console.log(msg())
```

编译以上代码，得到以下 JavaScript 代码：

```js
var msg = function () {
    return "hello world";
};
console.log(msg());
```

输出结果为：

```
hello world
```

带参数匿名函数：

```ts
var res = function(a:number,b:number) {
    return a*b;
};
console.log(res(12,2))
```

编译以上代码，得到以下 JavaScript 代码：

```js
var res = function (a, b) {
    return a * b;
};
console.log(res(12, 2));
```

输出结果为：

```
24
```

##### 匿名函数自调用

匿名函数自调用在函数后使用 () 即可：

```ts
(function () {
    var x = "Hello!!";
    console.log(x)
})()
```

编译以上代码，得到以下 JavaScript 代码：

```js
(function () {
    var x = "Hello!!";
    console.log(x)
})()
```

输出结果为：

```
Hello!!
```

#### 构造函数

TypeScript也支持使用JavaScript内置的构造函数Function()来定义函数：

语法格式如下：

```ts
var res = new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

参数说明：

- **arg1, arg2, ... argN**：参数列表。
- **functionBody**：一个含有包括函数定义的 JavaScript 语句的字符串。

实例

```ts
var myFunction = new Function("a", "b", "return a * b"); 
var x = myFunction(4, 3); 
console.log(x);
```

编译以上代码，得到以下JavaScript代码：

```js
var myFunction = new Function("a", "b", "return a * b"); 
var x = myFunction(4, 3); 
console.log(x);
```

输出结果为：

```
12
```



#### Lambda函数

Lambda 函数也称之为箭头函数。

箭头函数表达式的语法比函数表达式更短。

函数只有一行语句：

```js
( [param1, param2,…param n] )=>statement;
```

实例

以下实例声明了 lambda 表达式函数，函数返回两个数的和：

```ts
var foo = (x:number)=>10 + x 
console.log(foo(100))      //输出结果为 110
```

编译以上代码，得到以下 JavaScript 代码：

```js
var foo = function (x) { return 10 + x; };
console.log(foo(100)); //输出结果为 110
```

输出结果为：

```
110
```

函数是一个语句块：

```
( [param1, param2,…param n] )=> {
 
    // 代码块
}
```



单个参数 **()** 是可选的：

```ts
var display = x => {
    console.log("输出为 "+x)
}
display(12)
```

编译以上代码，得到以下 JavaScript 代码：

```js
var display = function (x) {
    console.log("输出为 " + x);
};
display(12);
```

输出结果为：

```
输出为 12
```

无参数时可以设置空括号：

```ts
var disp =()=> {
    console.log("Function invoked");
}
disp();
```

编译以上代码，得到以下 JavaScript 代码：

```js
var disp = function () {
    console.log("调用函数");
};
disp();
```

输出结果为：

```
调用函数
```



#### 函数重载

重载是方法名字相同，而参数不同，返回类型可以相同也可以不同。

每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。

参数类型不同：

```
function disp(string):void; 
function disp(number):void;
```

参数数量不同：

```
function disp(n1:number):void; 
function disp(x:number,y:number):void;
```

参数类型顺序不同：

```
function disp(n1:number,s1:string):void; 
function disp(s:string,n:number):void;
```

如果参数类型不同，则参数类型应设置为 **any**。

参数数量不同你可以将不同的参数设置为可选。

实例

以下实例定义了参数类型与参数数量不同：

```ts
function disp(s1:string):void;
function disp(n1:number,s1:string):void;
function disp(x:any,y?:any):void {
    console.log(x);
    console.log(y);
}
disp("abc")
disp(1,"xyz");
```

编译以上代码，得到以下 JavaScript 代码：

```js
function disp(x, y) {
    console.log(x);
    console.log(y);
}
disp("abc"); 
disp(1, "xyz");
```

输出结果为：

```
abc
undefined
1
xyz
```



### TypeScript Number

TypeScript与JavaScript类似，支持Number对象

Number对象是原始数值的包装对象。

语法

```ts
var num = new Number(value);
```

注意：如果一个参数值不能转换成一个数字将返回NaN（非数字值）

| 属性              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| MAX_VALUE         | 可表示的最大的数，MAX_VALUE 属性值接近于 1.79E+308。大于 MAX_VALUE 的值代表 "Infinity"。 |
| MIN_VALUE         | 可表示的最小的数，即最接近 0 的正数 (实际上不会变成 0)。最大的负数是 -MIN_VALUE，MIN_VALUE 的值约为 5e-324。小于 MIN_VALUE ("underflow values") 的值将会转换为 0。 |
| NaN               | 非数字值（Not-A-Number）。                                   |
| NEGATIVE_INFINITY | 负无穷大，溢出时返回该值。该值小于 MIN_VALUE。               |
| POSITIVE_INFINITY | 正无穷大，溢出时返回该值。该值大于 MAX_VALUE。               |
| prototype         | Number 对象的静态属性。使您有能力向对象添加属性和方法。      |
| constructor       | 返回对创建此对象的 Number 函数的引用。                       |

```ts
console.log("TypeScript Number 属性: "); 
console.log("最大值为: " + Number.MAX_VALUE); 
console.log("最小值为: " + Number.MIN_VALUE); 
console.log("负无穷大: " + Number.NEGATIVE_INFINITY); 
console.log("正无穷大:" + Number.POSITIVE_INFINITY);
```

输出结果为：

```
TypeScript Number 属性:
最大值为: 1.7976931348623157e+308
最小值为: 5e-324
负无穷大: -Infinity
正无穷大:Infinity
```



#### NaN实例

```ts
var month = 0 
if( month<=0 || month >12) { 
    month = Number.NaN 
    console.log("月份是："+ month) 
} else { 
    console.log("输入月份数值正确。") 
}
```

输出结果为：

```
月份是：NaN
```



### prototype 实例

```ts
function employee(id:number,name:string) {
    this.id = id
    this.name = name
}
var emp = new employee(123,"admin")
employee.prototype.email = "admin@runoob.com"
console.log("员工号: "+emp.id)
console.log("员工姓名: "+emp.name)
console.log("员工邮箱: "+emp.email)
```

输出结果为：

```
员工号: 123
员工姓名: admin
员工邮箱: admin@runoob.com
```



#### Number对象方法

Number对象支持一下方法：

| 方法 & 描述                                                  | 实例                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| toExponential()<br />把对象的值转换为指数计数法。            | //toExponential() <br/>var num1 = 1225.30 <br/>var val = num1.toExponential(); <br/>console.log(val) // 输出： 1.2253e+3 |
| toFixed()<br />把数字转换为字符串，并对小数点指定位数。      | var num3 = 177.234 <br/>console.log("num3.toFixed() 为 "+num3.toFixed())    // 输出：177<br/>console.log("num3.toFixed(2) 为 "+num3.toFixed(2))  // 输出：177.23<br/>console.log("num3.toFixed(6) 为 "+num3.toFixed(6))  // 输出：177.234000 |
| toLocaleString()<br />把数字转换为字符串，使用本地数字格式顺序。 | var num = new Number(177.1234); <br/>console.log( num.toLocaleString());  // 输出：177.1234 |
| toPrecision()<br />把数字格式化为指定的长度。                | var num = new Number(7.123456); <br/>console.log(num.toPrecision());  // 输出：7.123456 <br/>console.log(num.toPrecision(1)); // 输出：7<br/>console.log(num.toPrecision(2)); // 输出：7.1 |
| toString()<br />把数字转换为字符串，使用指定的基数。数字的基数是 2 ~ 36 之间的整数。若省略该参数，则使用基数 10。 | var num = new Number(10); <br/>console.log(num.toString());  // 输出10进制：10<br/>console.log(num.toString(2)); // 输出2进制：1010<br/>console.log(num.toString(8)); // 输出8进制：12 |
| valueOf()<br />返回一个 Number 对象的原始数字值。            | var num = new Number(10); <br/>console.log(num.valueOf()); // 输出：10 |



### TypeScript String（字符串）

String对象属性

| 方法 & 描述                         |      |
| ----------------------------------- | ---- |
| constructor对创建该对象的函数的引用 |      |
| length返回字符串的长度              |      |
| prototype允许您向对象添加属性和方法 |      |

#### String方法

下表列出了String对象支持的方法：

| 方法 & 描述                                                  |      |
| ------------------------------------------------------------ | ---- |
| charAt()<br />返回在指定位置的字符                           |      |
| charCodeAt()<br />返回在指定的位置的字符的Unicode编码        |      |
| concat()<br />连接两个或更多字符串，并返回新的字符串         |      |
| indexOf()<br />返回某个指定的字符串值在字符串中首次出现的位置。 |      |
| lastIndexOf()<br />从后向前搜索字符串，并从起始位置（0）开始计算返回字符串最后出现的位置。 |      |
| localeCompare()<br />用本地特定的顺序来比较两个字符串。      |      |
| match()<br />查找找到一个或多个正则表达式的匹配。            |      |
| replace()<br />替换与正则表达式匹配的子串                    |      |
| search()<br />检索与正则表达式相匹配的值                     |      |
| slice()<br />提取字符串的片断，并在新的字符串中返回被提取的部分 |      |
| split()<br />把字符串分割为子字符串数组。                    |      |
| substr()<br />从起始索引号提取字符串中指定数目的字符。       |      |
| substring()<br />提取字符串中两个指定的索引号之间的字符。    |      |
| toLocaleLowerCase()<br />根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射。 |      |
| toLocaleUpperCase()<br />据主机的语言环境把字符串转换为大写，只有几种语言（如土耳其语）具有地方特有的大小写映射。 |      |
| toLowerCase()<br />把字符串转换为小写。                      |      |
| toString()<br />返回字符串。                                 |      |
| toUpperCase()<br />把字符串转换为大写。                      |      |
| valueOf()<br />返回指定字符串对象的原始值。                  |      |



### TypeScript Array(数组)

TypeScript 声明数组的语法格式如下所示：

```ts
var array_name[:datatype];        //声明 
array_name = [val1,val2,valn..]   //初始化
```

或者直接在声明时初始化：

```ts
var array_name[:datatype] = [val1,val2…valn]
```

如果数组声明时未设置类型，则会被认为是 any 类型，在初始化时根据第一个元素的类型来推断数组的类型。

实例

创建一个 number 类型的数组：

```ts
var numlist:number[] = [2,4,6,8]
```



#### Array 对象

我们也可以使用 Array 对象创建数组。

Array 对象的构造函数接受以下两种值：

- 表示数组大小的数值。
- 初始化的数组列表，元素使用逗号分隔值。

实例

指定数组初始化大小：

```ts
var arr_names:number[] = new Array(4)  
 
for(var i = 0; i<arr_names.length; i++) {
    arr_names[i] = i * 2
    console.log(arr_names[i]) 
}
```

输出结果为：

```
0
2
4
6
```

以下实例我们直接初始化数组元素：

```ts
var sites:string[] = new Array("Google","Runoob","Taobao","Facebook")   for(var i = 0;i<sites.length;i++) {
    console.log(sites[i])
}
```

输出结果为：

```
Google
Runoob
Taobao
Facebook
```

#### 数组解构

我们也可以把数组元素赋值给变量，如下所示：

```ts
var arr:number[] = [12,13] 
var[x,y] = arr // 将数组的两个元素赋值给变量 x 和 y
console.log(x) 
console.log(y)
```

输出结果为：

```
12
13
```

#### 数组迭代

我们可以使用 for 语句来循环输出数组的各个元素：

```ts
var j:any; 
var nums:number[] = [1001,1002,1003,1004] 
 
for(j in nums) { 
    console.log(nums[j]) 
}
```

输出结果为：

```
1001
1002
1003
1004
```

#### 多维数组

一个数组的元素可以是另外一个数组，这样就构成了多维数组（Multi-dimensional Array）。

最简单的多维数组是二维数组，定义方式如下：

```ts
var arr_name:datatype[][]=[ [val1,val2,val3],[v1,v2,v3] ]
```

实例

定义一个二维数组，每一个维度的数组有三个元素。

```ts
var multi:number[][] = [[1,2,3],[23,24,25]]  
console.log(multi[0][0]) 
console.log(multi[0][1]) 
console.log(multi[0][2]) 
console.log(multi[1][0]) 
console.log(multi[1][1]) 
console.log(multi[1][2])
```



#### 数组在函数中的使用

##### 作为参数传递给函数

```ts
var sites:string[] = new Array("Google","Runoob","Taobao","Facebook") 
 
function disp(arr_sites:string[]) {
    for(var i = 0;i<arr_sites.length;i++) { 
        console.log(arr_sites[i]) 
    }  
}  
disp(sites);
```

输出结果为：

```
Google
Runoob
Taobao
Facebook
```

##### 作为函数的返回值

```ts
function disp():string[] { 
    return new Array("Google", "Runoob", "Taobao", "Facebook");
} 
 
var sites:string[] = disp() 
for(var i in sites) { 
    console.log(sites[i]) 
}
```

输出结果为：

```
Google
Runoob
Taobao
Facebook
```

#### 数组方法

下表列出了一些常用的数组方法：

|      |      |
| ---- | ---- |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |
|      |      |





### TypeScript Map 对象



### TypeScript 元组



### TypesScript 联合类型

联合类型（Union Types）可以通过管道(|)将变量设置多种类型，赋值时可以根据设置的类型来赋值。

**注意**：只能赋值指定的类型，如果赋值其它类型就会报错。

创建联合类型的语法格式如下：

```ts
Type1|Type2|Type3 
```

声明一个联合类型：

```ts
var val:string|number
val = 12
console.log("数字为 "+ val)
val = "Runoob"
console.log("字符串为 " + val)
```

输出结果为：

```
数字为 12
字符串为 Runoob
```

如果赋值其它类型就会报错：

```ts
var val:string|number 
val = true 
```

也可以将联合类型作为函数参数使用：

```ts
function disp(name:string|string[]) {
    if(typeof name == "string") {
        console.log(name)
    } else {
        var i;
        for(i = 0;i<name.length;i++) {
            console.log(name[i])
        }
    }
}
disp("Runoob")
console.log("输出数组....")
disp(["Runoob","Google","Taobao","Facebook"])
```

输出结果为：

```
Runoob
输出数组....
Runoob
Google
Taobao
Facebook
```

#### 联合类型数组

我们也可以将数组声明为联合类型：

```ts
var arr:number[]|string[];
var i:number;
arr = [1,2,4]
console.log("**数字数组**")
for(i = 0;i<arr.length;i++) {
    console.log(arr[i])
}
arr = ["Runoob","Google","Taobao"]
console.log("**字符串数组**")
for(i = 0;i<arr.length;i++) {
    console.log(arr[i])
}
```

输出结果为：

```
**数字数组**
1
2
4
**字符串数组**
Runoob
Google
Taobao
```



### TypeScript 接口

> **TS中的接口的特殊性**：
>
> 除了用作类的规范之外，也常用于直接描述对象的类型，例如，现有一个变量的定义如下：
>
> ```ts
> interface Person {
>     name: string;
>     age: number;
>     gender: string;
> }
> let person: Person = {name: '张三', age: 10, gender: '男'};
> ```

接口是一系列抽象方法的声明，是一些方法特征的集合，这些方法都应该是抽象的，需要由具体的类去实现，然后第三方就可以通过这组抽象方法调用，让具体的类执行具体的方法。

TypeScript 接口定义如下：

```ts
interface interface_name { 
}
```

以下实例中，我们定义了一个接口 IPerson，接着定义了一个变量 customer，它的类型是 IPerson。

customer 实现了接口 IPerson 的属性和方法。

```ts
interface IPerson { 
    firstName:string, 
    lastName:string, 
    sayHi: ()=>string 
} 
 
var customer:IPerson = { 
    firstName:"Tom",
    lastName:"Hanks", 
    sayHi: ():string =>{return "Hi there"} 
} 
 
console.log("Customer 对象 ") 
console.log(customer.firstName) 
console.log(customer.lastName) 
console.log(customer.sayHi())  
 
var employee:IPerson = { 
    firstName:"Jim",
    lastName:"Blakes", 
    sayHi: ():string =>{return "Hello!!!"} 
} 
 
console.log("Employee  对象 ") 
console.log(employee.firstName) 
console.log(employee.lastName)
```

需要注意接口不能转换为 JavaScript。 它只是 TypeScript 的一部分。

输出结果为：

```
Customer 对象
Tom
Hanks
Hi there
Employee  对象
Jim
Blakes
```

#### 联合类型和接口

以下实例演示了如何在接口中使用联合类型：

```ts
interface RunOptions {
    program:string;
    commandline:string[]|string|(()=>string);
}

// commandline 是字符串
var options:RunOptions = {program:"test1",commandline:"Hello"};
console.log(options.commandline)

// commandline 是字符串数组
options = {program:"test1",commandline:["Hello","World"]};
console.log(options.commandline[0]);
console.log(options.commandline[1]);

// commandline 是一个函数表达式
options = {program:"test1",commandline:()=>{return "**Hello World**";}};

var fn:any = options.commandline;
console.log(fn());
```

输出结果为：

```
Hello
Hello
World
**Hello World**
```



#### 接口和数组

接口中我们可以将数组的**索引值和元素设置为不同类型**，**索引值可以是数字或字符串**。

设置元素为字符串类型：

```ts
interface namelist {
    [index:number]:string
}

// 类型一致，正确
var list2:namelist = ["Google","Runoob","Taobao"]
// 错误元素 1 不是 string 类型
var list2:namelist = ["Runoob",1,"Taobao"]
```

如果使用了其他类型会报错：

```ts
interface namelist {
    [index:number]:string
}

// 类型一致，正确
var list2:namelist = ["Google","Runoob","Taobao"]
// 错误元素 1 不是 string 类型
var list2:namelist = ["John",1,"Bran"]
```

执行后报错如下，显示类型不一致：

```
test.ts:8:30 - error TS2322: Type 'number' is not assignable to type 'string'.

8 var list2:namelist = ["John",1,"Bran"]
                               ~

  test.ts:2:4
    2    [index:number]:string
         ~~~~~~~~~~~~~~~~~~~~~
    The expected type comes from this index signature.


Found 1 error.
```



```ts
interface ages {
    [index:string]:number
}

var agelist:ages;
// 类型正确
agelist["runoob"] = 15

// 类型错误，输出  error TS2322: Type '"google"' is not assignable to type 'number'. 
// agelist[2] = "google"
```



#### 接口继承

接口继承就是说接口可以通过其他接口来扩展自己。

Typescript 允许接口继承多个接口。

继承使用关键字 **extends**。

单接口继承语法格式：

```ts
Child_interface_name extends super_interface_name
```

多接口继承语法格式：

```ts
Child_interface_name extends super_interface1_name, super_interface2_name,…,super_interfaceN_name
```

继承的各个接口使用逗号 **,** 分隔。

**单继承实例**

```ts
interface Person { 
   age:number 
} 
 
interface Musician extends Person { 
   instrument:string 
} 
 
var drummer = <Musician>{}; 
drummer.age = 27 
drummer.instrument = "Drums" 
console.log("年龄:  "+drummer.age)
console.log("喜欢的乐器:"+drummer.instrument)
```

输出结果为：

```
年龄:  27
喜欢的乐器:  Drums
```

**多继承实例**

```ts
interface IParent1 { 
    v1:number 
} 
 
interface IParent2 { 
    v2:number 
} 
 
interface Child extends IParent1, IParent2 { } 
var Iobj:Child = { v1:12, v2:23} 
console.log("value 1: "+Iobj.v1+" value 2: "+Iobj.v2)
```

输出结果为：

```
value 1: 12 value 2: 23
```



### TypeScript 类

TypeScript 是面向对象的 JavaScript。

类描述了所创建的对象共同的属性和方法。

TypeScript 支持面向对象的所有特性，比如 类、接口等。

TypeScript 类定义方式如下：

```ts
class class_name { 
    // 类作用域
}
```

定义类的关键字为 class，后面紧跟类名，类可以包含以下几个模块（类的数据成员）：

- **字段** − 字段是类里面声明的变量。字段表示对象的有关数据。
- **构造函数** − 类实例化时调用，可以为类的对象分配内存。
- **方法** − 方法为对象要执行的操作。

实例

创建一个Person类：

```ts
class Person {
}
```

#### 创建类的数据成员

以下实例我们声明了类 Car，包含字段为 engine，构造函数在类实例化后初始化字段 engine。

this 关键字表示当前类实例化的对象。注意构造函数的参数名与字段名相同，this.engine 表示类的字段。

此外我们也在类中定义了一个方法 disp()。

```ts
class Car { 
    // 字段 
    engine:string; 
 
    // 构造函数 
    constructor(engine:string) { 
        this.engine = engine 
    }  
 
    // 方法 
    disp():void { 
        console.log("发动机为 :   "+this.engine) 
    } 
}
```

#### 创建实例化对象

我们使用 new 关键字来实例化类的对象，语法格式如下：

```
var object_name = new class_name([ arguments ])
```

类实例化时会调用构造函数，例如：

```
var obj = new Car("Engine 1")
```

类中的字段属性和方法可以使用 **.** 号来访问：

```
// 访问属性
obj.field_name 

// 访问方法
obj.function_name()
```

以下实例创建来一个 Car 类，然后通过关键字 new 来创建一个对象并访问属性和方法：

```ts
class Car {
    // 字段
    engine:string;
    // 构造函数
    constructor(engine:string) {
        this.engine = engine
    }        
    // 方法
    disp():void {
        console.log("函数中显示发动机型号:"+this.engine)
    }
}
// 创建一个对象
var obj = new Car("XXSY1")
// 访问字段
console.log("读取发动机型号 :  "+obj.engine)
// 访问方法
obj.disp()
```

输出结果为：

```
读取发动机型号 :  XXSY1
函数中显示发动机型号  :   XXSY1
```

#### 类的继承

TypeScript 支持继承类，即我们可以在创建类的时候继承一个已存在的类，这个已存在的类称为父类，继承它的类称为子类。

类继承使用关键字 **extends**，子类除了不能继承父类的私有成员(方法和属性)和构造函数，其他的都可以继承。

**TypeScript 一次只能继承一个类，不支持继承多个类，但 TypeScript 支持多重继承（A 继承 B，B 继承 C）**。

语法格式如下：

```ts
class child_class_name extends parent_class_name
```

类的继承：实例中创建了 Shape 类，Circle 类继承了 Shape 类，Circle 类可以直接使用 Area 属性：

```ts
class Shape { 
   Area:number 
   
   constructor(a:number) { 
      this.Area = a 
   } 
} 
 
class Circle extends Shape { 
   disp():void { 
      console.log("圆的面积:  "+this.Area) 
   } 
}
  
var obj = new Circle(223); 
obj.disp()
```

输出结果为：

```
圆的面积:  223
```

需要注意的是子类只能继承一个父类，TypeScript 不支持继承多个类，但支持多重继承，如下实例：

```ts
class Root { 
   str:string; 
} 
 
class Child extends Root {} 
class Leaf extends Child {} // 多重继承，继承了 Child 和 Root 类
 
var obj = new Leaf(); 
obj.str ="hello" 
console.log(obj.str)
```

输出结果为：

```
hello
```

#### 继承类的方法重写

类继承后，子类可以对父类的方法重新定义，这个过程称之为方法的重写。

其中 super 关键字是对父类的直接引用，该关键字可以引用父类的属性和方法。

```ts
class PrinterClass { 
   doPrint():void {
      console.log("父类的 doPrint() 方法。") 
   } 
} 
 
class StringPrinter extends PrinterClass { 
   doPrint():void { 
      super.doPrint() // 调用父类的函数
      console.log("子类的 doPrint()方法。")
   } 
}
```

输出结果为：

```
父类的 doPrint() 方法。
子类的 doPrint()方法。
```

#### static 关键字

static 关键字用于定义类的数据成员（属性和方法）为静态的，静态成员可以直接通过类名调用。

```ts
class StaticMem {
    static num:number;
    static disp():void {
        console.log("num 值为 "+ StaticMem.num)
    }
}
StaticMem.num = 12     // 初始化静态变量
StaticMem.disp()       // 调用静态方法
```

输出结果为：

```
num 值为 12
```

#### instanceof 运算符

instanceof 运算符用于判断对象是否是指定的类型，如果是返回 true，否则返回 false。

```ts
class Person{ }
var obj = new Person()
var isPerson = obj instanceof Person;
console.log("obj 对象是 Person 类实例化来的吗？ " + isPerson);
```

输出结果为：

```
obj 对象是 Person 类实例化来的吗？ true
```

#### 访问控制修饰符

TypeScript 中，可以使用访问控制符来保护对类、变量、方法和构造方法的访问。TypeScript 支持 3 种不同的访问权限。

- **public（默认）** : 公有，可以在任何地方被访问。
- **protected** : 受保护，可以被其自身以及其子类访问。
- **private** : 私有，只能被其定义所在的类访问。

以下实例定义了两个变量 str1 和 str2，str1 为 public，str2 为 private，实例化后可以访问 str1，如果要访问 str2 则会编译错误。

```ts
class Encapsulate {
    str1:string = "hello"
    private str2:string = "world"
}
var obj = new Encapsulate()
console.log(obj.str1)     // 可访问
console.log(obj.str2)   // 编译错误， str2 是私有的
```

#### 类和接口

类可以实现接口，使用关键字 implements，并将 interest 字段作为类的属性使用。

以下实例中 AgriLoan 类实现了 ILoan 接口：

```ts
interface ILoan { 
   interest:number 
} 
 
class AgriLoan implements ILoan { 
   interest:number 
   rebate:number 
   
   constructor(interest:number,rebate:number) { 
      this.interest = interest 
      this.rebate = rebate 
   } 
} 
 
var obj = new AgriLoan(10,1) 
console.log("利润为 : "+obj.interest+"，抽成为 : "+obj.rebate )
```

输出结果为：

```
利润为 : 10，抽成为 : 1
```



### TypeScript 对象

对象是包含一组键值对的实例。 值可以是标量、函数、数组、对象等，如下实例：

```ts
var object_name = { 
    key1: "value1", // 标量
    key2: "value",  
    key3: function() {
        // 函数
    }, 
    key4:["content1", "content2"] //集合
}
```

以上对象包含了标量，函数，集合(数组或元组)。

```ts
var sites = { 
   site1:"Runoob", 
   site2:"Google" 
}; 
// 访问对象的值
console.log(sites.site1) 
console.log(sites.site2)
```

输出结果为：

```
Runoob
Google
```

#### TypeScript 类型模板

假如我们在 JavaScript 定义了一个对象：

```ts
var sites = {
    site1:"Runoob",
    site2:"Google"
};
```

这时如果我们想在对象中添加方法，可以做以下修改：

```ts
sites.sayHello = function(){ return "hello";}
```

如果在 TypeScript 中使用以上方式则会出现编译错误，因为Typescript 中的对象必须是特定类型的实例。

```ts
var sites = {
    site1: "Runoob",
    site2: "Google",
    sayHello: function () { } // 类型模板
};
sites.sayHello = function () {
    console.log("hello " + sites.site1);
};
sites.sayHello();
```

输出结果为：

```
hello Runoob
```

此外对象也可以作为一个参数传递给函数，如下实例：

```ts
var sites = {
    site1:"Runoob",
    site2:"Google",
};
var invokesites = function(obj: { site1:string, site2 :string }) {
    console.log("site1 :"+obj.site1)
    console.log("site2 :"+obj.site2)
}
invokesites(sites)
```

输出结果为：

```
site1 :Runoob
site2 :Google
```



### TypeScript 泛型

泛型（Generics是一种编程语言特性，允许在定义函数、类、接口等使用占位符来表示类型，而不是具体的类型）。

泛型是一种在编程可重用、灵活且类型安全的代码时非常有用的功能。

使用泛型的主要目的是为了处理不特定类型的数据，使得代码可以适用于多种数据类型而不失去类型检查。

泛型的优势包括：

+ **代码重用：** 可以编写与特定类型无关的通用代码，提高代码的复用性。
+ **类型安全：** 在编译时进行类型检查，避免在运行时出现类型错误。
+ **抽象性：** 允许编写更抽象和通用的代码，适应不同的数据类型和数据结构。

#### 泛型标识符

在泛型中，通常使用一些约定俗成的标识符，比如常见的 `T`（表示 Type）、`U`、`V` 等，但实际上你可以使用任何标识符。

**T**: 代表 "Type"，是最常见的泛型类型参数名。

```ts
function identity<T>(arg: T): T {
    return arg;
}
```

**K, V**: 用于表示键（Key）和值（Value）的泛型类型参数。

```ts
interface KeyValuePair<K, V> {
    key: K;
    value: V;
}
```

**E**: 用于表示数组元素的泛型类型参数。

```ts
function printArray<E>(arr: E[]): void {
    arr.forEach(item => console.log(item));
}
```

**R**: 用于表示函数返回值的泛型类型参数。

```ts
function getResult<R>(value: R): R {
    return value;
}
```

**U, V**: 通常用于表示第二、第三个泛型类型参数。

```ts
function combine<U, V>(first: U, second: V): string {
    return `${first} ${second}`;
}
```

这些标识符是约定俗成的，实际上你可以选择任何符合标识符规范的名称。关键是使得代码易读和易于理解，所以建议在泛型类型参数上使用描述性的名称，以便于理解其用途。

#### 泛型函数

#### 泛型接口

#### 泛型类

#### 泛型约束

#### 泛型与默认值



### TypeScript 命名空间

命名空间一个最明确的目的就是解决重名问题。

假设这样一种情况，当一个班上有两个名叫小明的学生时，为了明确区分它们，我们在使用名字之外，不得不使用一些额外的信息，比如他们的姓（王小明，李小明），或者他们父母的名字等等。

命名空间定义了标识符的可见范围，一个标识符可在多个命名空间中定义，它在不同命名空间中的含义是互不相干的。这样，在一个新的命名空间中可定义任何标识符，它们不会与任何已有的标识符发生冲突，因为已有的定义都处于其他命名空间中。

TypeScript 中命名空间使用 **namespace** 来定义，语法格式如下：

```ts
namespace SomeNameSpaceName {
    export interface ISomeInterfaceName {	}
    export class SomeClassName {	}
}
```

以上定义了一个命名空间 SomeNameSpaceName，如果我们需要在外部可以调用 SomeNameSpaceName 中的类和接口，则需要在类和接口添加 **export** 关键字。

要在另外一个命名空间调用语法格式为：

```ts
SomeNameSpaceName.SomeClassName;
```

如果一个命名空间在一个单独的 TypeScript 文件中，则应使用三斜杠 /// 引用它，语法格式如下：

```ts
/// <reference path = "SomeFileName.ts" />
```

以下实例演示了命名空间的使用，定义在不同文件中：

IShape.ts文件代码：

```ts
namespace Drawing { 
    export interface IShape { 
        draw(); 
    }
}
```

Circle.ts文件代码：

```ts
/// <reference path = "IShape.ts" /> 
namespace Drawing { 
    export class Circle implements IShape { 
        public draw() { 
            console.log("Circle is drawn"); 
        }  
    }
}
```

Triangle.ts文件代码：

```ts
/// <reference path = "IShape.ts" /> 
namespace Drawing { 
    export class Triangle implements IShape { 
        public draw() { 
            console.log("Triangle is drawn"); 
        } 
    } 
}
```

TestShape.ts文件代码：

```ts
/// <reference path = "IShape.ts" />   
/// <reference path = "Circle.ts" /> 
/// <reference path = "Triangle.ts" />  
function drawAllShapes(shape:Drawing.IShape) { 
    shape.draw(); 
} 
drawAllShapes(new Drawing.Circle());
drawAllShapes(new Drawing.Triangle());
```

使用 node 命令查看输出结果为：

```
$ node app.js
Circle is drawn
Triangle is drawn
```

#### 嵌套命名空间

命名空间支持嵌套，即你可以将命名空间定义在另外一个命名空间里头。

```ts
namespace namespace_name1 {
    export namespace namespace_name2 {
        export class class_name {    }
    }
}
```

成员的访问使用点号 **.** 来实现，如下实例：

Invoice.ts 文件代码：

```ts
namespace Runoob {
    export namespace invoiceApp {
        export class Invoice {
            public calculateDiscount(price: number) {
                return price * .40;
            }
        }
    }
}
```

InvoiceTest.ts 文件代码：

```ts
/// <reference path = "Invoice.ts" />
var invoice = new Runoob.invoiceApp.Invoice();
console.log(invoice.calculateDiscount(500));
```

使用 node 命令查看输出结果为：

```
$ node app.js
200
```



### TypeScript 模块

TypeScript 模块的设计理念是可以更换的组织代码。

模块是在其自身的作用域里执行，并不是在全局作用域，这意味着定义在模块里面的变量、函数和类等在模块外部是不可见的，除非明确地使用 export 导出它们。类似地，我们必须通过 import 导入其他模块导出的变量、函数、类等。

两个模块之间的关系是通过在文件级别上使用 import 和 export 建立的。

模块使用模块加载器去导入其它的模块。 在运行时，模块加载器的作用是在执行此模块代码前去查找并执行这个模块的所有依赖。 大家最熟知的JavaScript模块加载器是服务于 Node.js 的 CommonJS 和服务于 Web 应用的 Require.js。

此外还有有 SystemJs 和 Webpack。

模块导出使用关键字 **export** 关键字，语法格式如下：

```ts
// 文件名 : SomeInterface.ts 
export interface SomeInterface { 
   // 代码部分
}
```

要在另外一个文件使用该模块就需要使用 **import** 关键字来导入:

```ts
import someInterfaceRef = require("./SomeInterface");
```

**实例**

IShape.ts文件代码：

```ts
/// <reference path = "IShape.ts" /> 
export interface IShape { 
   draw(); 
}
```

Circle.ts文件代码：

```ts
import shape = require("./IShape"); 
export class Circle implements shape.IShape { 
   public draw() { 
      console.log("Cirlce is drawn (external module)"); 
   } 
}
```

Triangle.ts文件代码：

```ts
import shape = require("./IShape"); 
export class Triangle implements shape.IShape { 
   public draw() { 
      console.log("Triangle is drawn (external module)"); 
   } 
}
```

TestShape.ts文件代码：

```ts
import shape = require("./IShape"); 
import circle = require("./Circle"); 
import triangle = require("./Triangle");  
 
function drawAllShapes(shapeToDraw: shape.IShape) {
   shapeToDraw.draw(); 
} 
 
drawAllShapes(new circle.Circle()); 
drawAllShapes(new triangle.Triangle());
```

输出结果为：

```
Cirlce is drawn (external module)
Triangle is drawn (external module)
```



#### 避免命名冲突

若多个模块中具有相同的变量、函数等内容，将这些内容导入到同一模块下就会出现命名冲突。

解决方法：

+ **导入重命名**

  ```ts
  import { hello as helloFromA, str as strFromA } from "./moduleA";
  import { hello as helloFromC, str as strFromC} from "./moduleC";
  
  helloFromA()
  console.log(strFromA)
  helloFromC()
  console.log(strFromC)
  ```

+ **创建模块对象**

  上述导入重命名的方式能够很好地解决命名冲突的问题，但是当冲突内容较多时，这种写法会比较冗长。除了导入重命名外，还可以将某个模块的内容统一导入到一个**模块对象**上，这样就能简洁有效解决冲突的问题了，具体语法如下

  ```ts
  import * as A from "./moduleA";
  import * as C from "./moduleC"
  
  A.hello();
  console.log(A.str);
  C.hello();
  console.log(C.str);
  ```

  







### TypeScript声明文件

TypeScript 作为 JavaScript 的超集，在开发过程中不可避免要引用其他第三方的 JavaScript 的库。虽然通过直接引用可以调用库的类和方法，但是却无法使用TypeScript 诸如类型检查等特性功能。为了解决这个问题，需要将这些库里的函数和方法体去掉后只保留导出类型声明，而产生了一个描述 JavaScript 库和模块信息的声明文件。通过引用这个声明文件，就可以借用 TypeScript 的各种特性来使用库文件了。

假如我们想使用第三方库，比如 jQuery，我们通常这样获取一个 id 是 foo 的元素：

```ts
$('#foo');
// 或
jQuery('#foo');
```

