## HTML中的JavaScript

JavaScript是通过\<script>元素插人到 HTML,页面中的。这个元素可用于把 JavaScript代码嵌入到HTML, 页面中，跟其他标记混合在一起，也可用于引人保存在外部文件中的 JavaScript。本章的重点可以总结如下。

+ 要包含外部 JavaScript 文件，必须将 src 属性设置为要包含文件的 URL。文件可以跟网页在同一台服务器上，也可以位于完全不同的域。
+ 所有\<script>元素会依照它们在网页中出现的次序被解释。在不使用 defer 和 async 属性的情况下，包含在\<script>元素中的代码必须严格按次序解释。
+ 对不推迟执行的脚本，浏览器必须解释完位于\<script>元素中的代码，然后才能继续渲染页面的剩余部分。为此，通常应该把\<script>元素放到页面末尾，介于主内容之后及\</boay>标签之前。
+ 可以使用 defer 属性把脚本推迟到文档渲染完毕后再执行。推迟的脚本原则上按照它们被列出的次序执行。
+ 可以使用 async 属性表示脚本不需要等待其他脚本，同时也不阻塞文档渲染，即异步加载。异步脚本不能保证按照它们在页面中出现的次序执行。
+ 通过使用\<noscript>元素，可以指定在浏览器不支持脚本时显示的内容。如果浏览器支持并启用脚本，则\<noscript>元素中的任何内容都不会被渲染。





## 语言基础

#### 声明风格及最佳实践

1. 不使用var
   使用let和const有助于提升代码质量，因为变量有了明确的作用域、声明位置，以及不变的值
2. const优先，let次之
   可以让浏览器运行时强制保持变量不变，也可以让静态代码分析工具提前发现不合法的赋值操作。



#### 数据类型

#### function

> **注意**	严格来讲，函数在 ECMAScript 中被认为是对象，并不代表一种数据类型。可是，函数也有自己特殊的属性。为此，就有必要通过 typeof 操作符来区分函数和其他对象。



#### Undefined

> **注意**	一般来说，**永远不用显式地给某个变量设置 undefined 值**。字面值 `undefined` 主要用于比较，而且在 ECMA-262 第 3 版之前是不存在的。增加这个特殊值的目的就是**为了正式明确空对象指针（null）和未初始化变量的区别**。

> **注意**	即使未初始化的变量会被自动赋予 undefined 值，但我们**仍然建议在声明变量的同时进行初始化**。这样，当 typeof 返回"undefined"时，你就会知道那是因为给定的变量尚未声明，而不是声明了但未初始化。

undefined是一个假值，可以用更简洁的方式检测它，不过要记住，也有很多其他可能的值同样是假值，所以一定要明确自己想检测的就是undefined这个字面值，而不仅仅是假值。



#### Null

逻辑上讲，null值表示一个空对象指针，所以给typeof传一个null会返回"object"

```js
let car = null;
console.log(typeof car); //"object"
```

定义将来要保存对象值的对象时，建议使用null来初始化，不要使用其他值。这样只要检查这个变量是不是null就可以知道这个变量是否在后来被重新赋予了一个对象的引用，比如：

```js
if (car!=null) {
    //car是一个对象的引用
}
```

undefined值是由null值派生而来的，因此ECMA-262将他们定义为表面上相等，如下：

```js
console.log(null==undefined); // true
```



#### Boolean

类型转换

| 数据类型 | 转换为true值           | 转换为false值  |
| -------- | ---------------------- | -------------- |
| Boolean  | true                   | false          |
| String   | 非空字符串             | ""（空字符串） |
| Number   | 非零数值（包括无穷值） | 0、NaN         |
| Object   | 任意对象               | null           |
| Undefine | N/A（不存在）          | undefined      |



#### Number

八进制前缀0，在严格模式下无效；

十六进制前缀0x（区分大小写）



##### 1、值的范围

ES可以表示的最小数值保存在Number.MIN_VALUE中，这个值在多数浏览器中是5e-324；可以表示的最大值保存在Number.MAX_VALUE中，这个值在多数浏览器中是1.7975931348623157e+308。如果某个计算得到的数值超出了JavaScript可以表示的范围，那么这个数值会被自动转换为一个特殊的Infinity（无穷）值。任何无法表示的负数以-Infinity（负无穷大）表示，任何无法表示的正数以Infinity（正无穷大）表示。

如果计算返回正Infinity或负Infinity，则该值不能在进一步用于任何计算。确定一个值是不是有限大，可以使用isFinite()函数。



##### 2、NaN

isNaN()判断这个参数是否不是数值。任何不能转换为数值的值都为导致这个函数返回true

```js
console.log(isNaN(NaN)); //true
console.log(isNaN(10)); //false
console.log(isNaN("10")); //false
console.log(isNaN("blue")); //true
console.log(isNaN(true)); //false,可以转换为数值1
```

> 虽然不常见，但isNaN可以用于测试对象。此时首先会调用对象的valueOf()方法，然后在确定返回的值是否可以转换为数值。如果不能，在调用toString()方法，并测试其返回值。这通常是ECMAScript内置函数和操作符的工作方式，本章后面会讨论



##### 3、数值转换

非数值转数值：Number()、parseInt()和parseFloat()。后两个主要用于字符串转换为数值。

Number()函数基于如下规则：

+ 布尔值：true转1，false转0
+ 数值：直接返回
+ null：返回0
+ undefined：返回NaN
+ 字符串：
  + 如果字符串包含数值，则转换为十进制数值（若前面有0忽略前面的0）
  + 如果包含浮点值，转换为相应浮点值
  + 如果包含有效十六进制格式如"0xf"，则会转换为与该十六进制对应的十进制整数值
  + 空字符串：返回0
  + 其他情况：返回NaN
+ 对象：调用valueOf（）方法，并按照上述规则转换返回的值。如果转换结果是NaN，则调用toString（）方法，再按照字符串的规则转换。



#### Object

每个Object实例都有如下属性和方法。

+ constructor：用于创建当前对象的函数。在前面的例子中，这个属性的值就是Object（）函数。
+ hasOwnProperty(propertyName)：用于判断当前对象实例（不是原型）上是否存在给定的属性。要检查的属性名必须是字符串（如o.hasOwnProperty("name")）或符号。
+ isPrototypeof(object)：用于判断当前对象是否为另一个对象的原型
+ propertyIsEnumerable(propertyName)：用于判断给定的属性是否可以使用for-in语句枚举。与hasOwnProperty()一样，属性名必须市场字符串。
+ toLocaleString()：返回对象的字符串表示，该字符串反映对象所在的本地化执行环境。
+ toString()：返回对象的字符串表示。
+ valueOf()：返回对象对应的字符串、数值或布尔值表示。通常与toString的返回值相同。



## 基本引用类型

### RegExp

语法

```js
let expression = /pattern/flags
```

**pattern**（模式）可以是任何简单或复杂的正则表达式

**flags**（标记）可以有零个或多个，用于控制正则表达式的行为。

+ g：全局模式，表示查找字符串的全部内容
+ i：不区分大小写，表示在查找匹配时忽略pattern和字符串的大小写。
+ m：多行模式，表示查找匹配时忽略pattern和字符串的大小写。
+ y：粘附模式，表示只查找从lastIndex开始及之后的字符串。
+ u：Unicode模式，启用Unicode匹配。
+ s：dotAll模式，表示元字符，匹配任何字符（包括\n或\r）。



**元字符**在模式中必须转义

`( [ { \ ^ $ | } ] ) ? * + .`



任何使用字面量定义的正则表达式也可以通过构造函数来创建，比如：

```js
// 匹配第一个"bat"或"cat"，忽略大小写
let pattern1 =  /[bc]at/i

// 跟pattern1一样，只不过是用构造函数创建的
let pattern2 = new RegExp("[bc]at", "i");
```

这里pattern1和pattern2是等效的正则表达式。



### 原始值包装类型

#### Boolean

强烈建议永远不要使用Boolean

#### Number

同Boolean类型，Number类型重写了valueOf()、toLocalString()和toString()方法。valueOf()方法返回Number对象表示的原始数值，另外两个方法返回数值字符串。`toString()`方法可选地接收一个表示基地的参数，并返回相应基数形式的字符串，如下：

```js
let num = 10;
console.log(num.toString());	// "10"
console.log(num.toString(2));	// "1010"
console.log(num.toString(8));	// "12"
console.log(num.toString(10));	// "10"
console.log(num.toString(16));	// "a"
```

`toFix()`返回包含指定小数点位数的数值字符串





#### String

JavaScript采用两种Unicode编码混合的策略：UCS-2和UTF-16。对于可以采用16为编码的字符（U+0000~U+FFFF），这两种编码实际上是一样的。

16位可以表示65536个字符，在Unicode中称为**基本多语言平面**，为了表示更多的字符，Unicode采用了一个策略，即每个字符使用另外16位去选择一个**增补平面**。这种每个字符使用两个16位码元的策略称为**代理对**。

**码点**是Unicode中一个字符的完整标识。比如"c"的码点事0x0063，而"☺"的码点是0x1F60A。码点可能是16位，也可能是32位，而codePointAt()方法可以从指定码元位置识别完整的码点。

```js
// "smiling face with smiling eyes" 表情符号的编码是 U+1F60A 
// 0x1F60A === 128522 
let message = "ab☺de"; 
console.log(message.length); // 6 
console.log(message.charAt(1)); // b
console.log(message.charAt(2)); // <?> 
console.log(message.charAt(3)); // <?> 
console.log(message.charAt(4)); // d 
console.log(message.charCodeAt(1)); // 98 
console.log(message.charCodeAt(2)); // 55357 
console.log(message.charCodeAt(3)); // 56842 
console.log(message.charCodeAt(4)); // 100 
console.log(String.fromCodePoint(0x1F60A)); // ☺
console.log(String.fromCharCode(97, 98, 55357, 56842, 100, 101)); // ab☺de
```

这些方法仍然将 16 位码元当作一个字符，事实上索引 2 和索引 3 对应的码元应该被看成一个代理对，只对应一个字符。fromCharCode()方法仍然返回正确的结果，因为它实际上是基于提供的二进制表示直接组合成字符串。浏览器可以正确解析代理对（由两个码元构成），并正确地将其识别为一个Unicode 笑脸字符。

为正确解析既包含单码元字符又包含代理对字符的字符串，可以使用 codePointAt()来代替charCodeAt()。跟使用 charCodeAt()时类似，codePointAt()接收 16 位码元的索引并返回该索引位置上的码点（code point）。码点是 Unicode 中一个字符的完整标识。比如，"c"的码点是 0x0063，而"☺"的码点是 0x1F60A。码点可能是 16 位，也可能是 32 位，而 codePointAt()方法可以从指定码元位置识别完整的码点。

```js
let message = "ab☺de"; 
console.log(message.codePointAt(1)); // 98 
console.log(message.codePointAt(2)); // 128522 
console.log(message.codePointAt(3)); // 56842 
console.log(message.codePointAt(4)); // 100
```

注意，如果传入的码元索引并非代理对的开头，就会返回错误的码点。这种错误只有检测单个字符的时候才会出现，可以通过从左到右按正确的码元数遍历字符串来规避。迭代字符串可以智能地识别代理对的码点：

```js
console.log([..."ab☺de"]); // ["a", "b", "☺", "d", "e"] 
```

与 charCodeAt()有对应的 codePointAt()一样，fromCharCode()也有一个对应的 fromCodePoint()。这个方法接收任意数量的码点，返回对应字符拼接起来的字符串：

```js
console.log(String.fromCharCode(97, 98, 55357, 56842, 100, 101)); // ab☺de 

console.log(String.fromCodePoint(97, 98, 128522, 100, 101)); // ab☺de 
```



> slice()、substring()第一个参数字符串开始的位置（包含该位置），第二个参数表示子字符串结束的位置（不包含该位置）
>
> substr()第一个参数字符串开始的位置（包含），第二个参数表示子字符串长度
>
> 
>
> slice()方法将所有负值参数都当成字符串长度加上负参数值。
>
> substr()方法将第一个负参数值当成字符串长度加上该负参数值，将第二个负参数值转换成0。
>
> substring()方法将所有的负参数值都转换成0。





## 集合引用类型

### 数组

ES给数组提供了几个方法，让它看起来像是另外一种数据结构

#### 栈方法

push()和pop()

#### 队列方法

shift()和push()

排序方法，reverse()和sort()
