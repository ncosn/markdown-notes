### TypeScript基础类型

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

