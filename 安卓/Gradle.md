## Groovy基础

### Groovy概述

Apache旗下的一种基于JVM的面向对象的编程语言，既可以用于面向对象编程，又可以用作纯粹的脚本语言。语言设计上西拿了Python、Ruby、和Smalltalk优秀特性，如动态类型转换、闭包和元编程支持。

Groovy与Java可以互相调用并结合编程，比如在写Groovy的时候忘了语法可以直接按Java语法写，也可以在Java中调用Groovy脚本。与Java相比，Groovy语法更加灵活简洁，可以用更少的代码来实现与Java实现相同的功能



### 编写和调试

Groovy的代码可以在Android Studio和IntelliJ IDEA等IDE中进行编写和调试，这种方式的缺点是需要配置环境，这里推荐在文本中编写代码并结合命令行调试（文本推荐Sublime Text）。

具体步骤：在一个目录中新建build.gradle文件，在build.gradle文件中新建一个task，在task中编写Groovy代码，用命令行进入这个build.gradle文件所在的目录，运行gradle task名称等命令行进行调试。



### 变量

用def关键字来定义变量，可以不指定变量的类型，默认访问修饰符是public。

```groovy
def a = 1;
def int b = 1;
def c = "hello world";
```



### 方法

使用返回类型或def关键字定义，方法可以接受任意数量的参数，这些参数可以不声明类型，如果不提供可见性修饰符，则该方法为public

```groovy
// 用def关键字定义方法
task method << {
    add (1,2)
    minus 1,2 //1
}
def add(int a, int b) {
    println a+b //2
}
def minus(a,b) {//3
    println a-b
}

// 如果指定了方法返回类型，则可以不需要def关键字来定义方法
task method << {
    def number = minus 1,2
    println number
}
int minus(a,b) {
    return a-b
}

// 如果不使用return，则方法的返回值为最后一行代码的执行结果
int minus(a,b) {
    a-b //4
}
```

从上面两段代码中可以知道Groovy中有很多省略的部分：

（1）语句后面的分号可以省略

（2）方法的括号可以省略，比如注释1处和注释2处

（3）参数类型可以省略，比如注释3处

（4）return可以省略，比如注释4处



### 类

Groovy类与Java类非常类似

```groovy
task method << {
    def p = new Person()
    p.increaseAge 5
    println p.age
}
class Person {
    String name
    Integer age = 10
    def increaseAge(Integer years) {
        this.age += years
    }
}
```

运行gradle method的打印结果为：15

Groovy类与Java类有以下区别：

（1）默认类的修饰符为public

（2）没有可见性修饰符的字段会自动生成对应的setter方法和getter方法

（3）类不需要与它的源文件有相同的名称，但还是建议采用相同的名称



### 语句

#### 断言

Groovy断言和Java断言不同，Groovy断言一直处于开启状态，是进行单元测试的首选方式。

```groovy
task method << {
    assert 1+2 == 6
}
```

输出结果为：

```
Execution failed for task ':method'.
> assert 1+2 == 6
        |  |
        3  false
```

当断言的条件为false时，程序会抛出异常，不再执行下面的代码，从输出可以清晰地看到发生错误的地方



#### for循环

Groovy支持Java的for(int i=0;i<N;i++)和for(int i:array)形式的循环语句，另外还支持for in loop形式，支持遍历范围、列表、Map、数组和字符串等多种类型。

```groovy
//遍历范围
def x = 0
for (i in 0..3) {
    x += i
}
assert x == 6
//遍历列表
def x = 0
for (i in [0,1,2,3]) {
    x += i
}
assert x == 6
//遍历Map中的值
def map = ['a':1, 'b':2, 'c':3]
x = 0
for (v in map.values()) {
    x += v
}
assert x == 6
```



#### switch语句

Groovy中的switch语句不仅能够兼容Java代码，还可以处理更多类型的case表达式

```groovy
task method << {
    def x =16
    def result = ""
    
    switch (x) {
        case "ok":
        	result = "found ok"
        case [1,2,4,'list']:
        	result = "list"
        	break
        case Integer:
            result = "integer"
        	break
        default:
            result = "default"
    }
    assert result == "range"
}
```

case表达式可以是字符串、列表、范围和Integer等，由于篇幅原因，这里只列举出了一小部分。



### 数据类型

主要有以下几种：

+ Java中的基本数据类型
+ Groovy中的容器类
+ 闭包



#### 字符串

Groovy的基本数据类型和Java大同小异，这里主要介绍字符串类型。在Groovy中有两种字符串，即普通字符串String（java.lang.GString）。其中普通字符串包含单引号字符串、双引号字符串、三引号字符串。

#### 单引号字符串

在Groovy中单引号字符串和双引号字符串都可以定义一个字符串常量，只不过单引号字符串不支持插值。

```groovy
'Android进阶解密'
```

#### 双引号字符串

想要插值可以使用双引号字符串，插值指的是替换字符串中的占位符，占位符表达式为${}，或者以$为前缀。

```groovy
def name = 'Android进阶之光'
println "hello ${name}"
println "hello $name"
```

#### 三引号字符串

三引号字符串可以保留文本的换行和缩进格式，不支持插值。

```groovy
task method << {
    def name = '''Android进阶之光
    	Android进阶解密
    Android进阶？'''
    println name
}
```

其打印结果为：

```
Android进阶之光
	Android进阶解密
Android进阶？
```

String是不可变的，GString却是可变的，GString和String即使有相同的字面量，他们的hashCode的值也可能不同，因此应该避免使用GString作为Map的key。



```groovy
assert "one:${1}".hashCode()!="one:1".hashCode()
```

当双引号字符串中包含插值表达式时，字符串类型为GString，因此上面的断言为true。



#### List

Groovy没有定义自己集合类，他在Java集合类的基础上进行了增强和简化。Groovy的List对应Java中的List接口，默认实现类为Java中的ArrayList。

```groovy
def number = [1,2,3]
assert number instanceof List
def linkedList = [1,2,3] as LinkedList
assert linedList instanceof java.util.LinkedList
```

可以使用as操作符来显示指定List的实现类为java.util.LinkedList

Groovy获取元素比Java要简洁些，使用[]来获取List具有正索引或负索引的元素。

```groovy
task method << {
    def number = [1,2,3,4]
    assert number[1] == 2
    assert number[-1] == 4 //1
    
    number << 5 //2
    assert number [4] == 5
    assert number [-1] == 5
}
```

在注释1处的索引-1是列表末尾的第一个元素。注释2处使用<<运算符在列表末尾追加一个元素



#### Map

创建Map同样使用[]，需要同时指定键和值，默认的实现类为java.util.LinkedHashMap。

```groovy
def name = [one:'魏无羡', two:'杨影枫', three:'张无忌']
assert name['one'] == '魏无羡'
assert name.two == '杨影枫'
```



Map还有一个键关联的问题：

```groovy
def key = 'name'
def person = [key:'魏无羡'] //1
assert person.containsKey('key')
person = [(key):'魏无羡'] //2
assert person.containsKey('name')
```

注释1处魏无羡的键值是key这个字符串而不是key变量变量的值name。如果想要以key变量的值键值，则需要像注释2处一样使用（key），用来告诉解析器我们传递的是一个变量，而不是定义一个字符串键值。



#### 闭包

Groovy中的闭包是一个开放的、匿名的、可以接受参数和返回值的代码块。

**1、定义闭包**

闭包的定义遵循以下语法。

```groovy
{ [closureParameters -> ] statements }
// 闭包分为两个部分，分别是参数列表部分[closureParamters ->]和语句部分statements。
```

参数列表部分是可选的，如果闭包只有一个参数，则参数名是可选的，Groovy会隐式指定it作为参数名，如下所示。

```groovy
{println it} // 使用隐式参数it的闭包
```

当需要指定参数列表时，需要用 -> 将参数列表和闭包体相分离，如下所示。

```groovy
{ it -> println it } //it是一个显示参数
{ String a, String b ->
    println "${a} is a ${b}"
}
```

闭包是groovy.lang.Cloush类的一个实例，可以将闭包赋值给变量或字段，如下所示。

```groovy
// 将闭包赋值给一个变量
def println = { it -> println it}
assert println instanceof Closure
// 将闭包赋值给Closure类型变量
Closure do = { println 'do!'}
```

**2、调用闭包**

闭包既可以当作方法来调用，又可以显示调用call方法

```groovy
def code = { 123 }
assert code() == 123		//闭包当作方法调用
assert code.call() == 123	//显示调用call方法
def isOddNumber = {int i -> i%2 != 0 }
assert isOddNumber(3) == true//调用带参数的闭包
```



### I/O操作

Groovy的I/O操作比Java更简洁

#### 文件读取

我们再PC上新建一个name.txt，在其中输入一些内容，然后用Groovy来读取该文件的内容。

```groovy
def filePath = "D:/Android/name.txt"
def file = new File(filePath);
file.eachLine {
    println it
}
```

可以看出Groovy文件读取很简洁，还可以更简洁些

```groovy
def filePath = "D:/Android/name.txt"
def file = new File(filePath);
println file.text
```

#### 文件写入

文件写入同样十分简洁

```groovy
def filePath = "D:/Android/name.txt"
def file = new File(filePath);

file.withPrintWriter {
    it.println("三井寿")
    it.println("仙道彰")
}
```



### 其他

#### asType

asType可以用于数据类型转换

```groovy
String a = '23'
int b = a as int
def c = a.asType(Integer)
assert c instance of java.lang.Integer
```

#### 判断是否为真

```groovy
if (name != null && name.length > 0) {}
```

可以替换为

```groovy
if (name) {}
```

#### 安全取值

在Java中，要安全获取某个对象的值可能需要大量的if语句来进行非空判断。

```groovy
if (school != null) {
    if (school.getStudent() != null) {
        if (school.getStudent().getName() != null) {
            System.out.println(school.getStudent.getName());
        }
    }
}
```

Groovy中可以使用`?.`来进行安全取值。

```groovy
println school?.student?.name
```

#### with操作符

对同一对象的属性进行赋值时，可以这样做。

```groovy
task method << {
    Person p = new Person()
    p.name = "杨影枫"
    p.age = 19
    p.sex = "男"
    println p.name
}
class Person {
    String name
    Integer age
    String sex
}
```

使用with来进行简化

```groovy
Person p = new Person()
p.with {
    name = "杨影枫"
    age = 19
    sex = "男"
}
println p.name
```





## Gradle核心

### Gradle概述

#### 项目自动化

Gradle是一个构建工具，为什么要用构建工具？这就需要从项目自动化开始讲起。

在开发软件，会临时相似的情况，我们需要用IDE来进行编码，当完成一些功能时会进行编译、单元测试和打包工作，这些都需要开发人员手动实现。而一般的软件都是迭代式开发，一个版本接着一个版本，每个版本又可能有很多功能，如果开发每次实现功能时都需要手动进行编译、单元测试和打包等工作，那么显然会非常耗时而且也容易出现问题，因此项目自动化应运而生，它有以下优点：

（1）项目自动化可以尽量防止开发手动接入从而节省了开发的时间并减少错误的发生

（2）项目自动化可以自定义有序的步骤来完成代码的编译、测试和打包工作，使重复的步骤变得简单

（3）IDE可能受到不同操作系统的限制，而自动化构建是不会依赖于特定的操作系统和IDE的，它具有平台无关性。

#### 构建工具

构建工具用于项目自动化，是一种可编程的工具，我们可以用代码来控制构建流程，最终生成可交付的软件。构建工具可以帮助我们创建一个重复的、可靠的、无需手动介入的、不依赖于特定操作系统和IDE的构建。这里以APK的构建过程来举例。

##### 1、APK的构建过程

APK的构建过程主要分为以下几个步骤

（1）通过AAPT（Android Asset Packaging Tool）打包res资源文件，比如AndroidManifest.xml和xml布局文件等，将这些xml文件编译为二进制，其中assets文件夹和raw文件夹的文件不会被编译为二进制，最终会生成R.java文件和resources.arsc文件。

（2）AIDL工具会将所有aidl接口转化为对应的Java接口。

（3）所有Java代码，包括R.java文件和Java接口都会被Java编译器编译成.class文件。

（4）Dex工具会将第（3）步生成的.class文件、第三方库和其他.class文件都编译成.dex文件

（5）第（4）步编译生成的.dex文件、编译过的资源、无需编译的资源都（如图片等）会被ApkBuilder工具打包成apk文件。

（6）使用Debug Keystore或者Release Keystore对第（5）步生成的apk文件进行签名。

（7）如果是对APK正式签名，那么还需要使用zipalign工具对APK进行对齐操作，这样应用运行时会减少内存的开销。

从以上步骤看出APK的构建过程是比较繁琐的，而且这个构建过程又是时常重复的，如果没有构建工具，手动完成构建工作，那么毫无疑问对于开发人员是个折磨，也会产生诸多问题，导致项目开发周期变长。

在Gradle出现之前，有三个基于Java的构建工具：Ant、Gant和Maven，他们被应用在Java或者Android开发中，我们来看看他们的特点。

##### 2、Apache Ant

Ant指的是Another Neat Tool。最初是用来构建Tomcat的，在2000年Ant成为一个独立的项目并被发布出来。Ant是由Java编写的构建工具，它的核心代码是由Java编写的，因此具有平台无关性，构建脚本是XML格式的（默认为build.xml）。

Ant构建脚本的样式如下所示：

build.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="test" default="hello">
    <echo message="running build.xml which is equivalent to build.gant"/>
    <property file="build.properties" />
    <target name="init" description="init target">
        <echo message="Executing init target" />
    </target>
    <tartget name="hello" depends="init" description="say hello target">
        <echo message="${echo.msg}" />
    </tartget>
</project>
```

Ant的构建脚本由三个基本元素组成：一个project（工程）、多个target（目标）和可用的task（任务）。

Apache Ant有以下缺点：

（1）Ant无法获取运行时的信息

（2）XML作为构建脚本的语言，如果构建逻辑复杂，那么构建脚本就会很长且难以维护

（3）Ant需要配合Ivy（一种管理项目依赖工具），否则Ant很难管理依赖

（4）Ant在如何组织项目结构方面没有给出任何指导，这导致Ant虽然灵活性高，但这样的灵活性导致每个构建脚本都是唯一的而且很难被理解。

##### 3、Gant

Gant是一个基于Ant的构建工具，它在Ant的基础上用Groovy写的DSL（领域特定语言）。如果用Ant实现构建，但是不喜欢用XML来编写构建脚本或者现有的XML构建脚本很难维护和管理，那么Gant是个不错的选择。

Gant构建文件的演示如下所示：

build.gant

```groovy
Ant.echo(message:'running build.gant')
Ant.property(file:'build.properties')
def antProperty = Ant.project.properties
target(init : 'init target') {
	echo(message : 'Executing init target')
}
target(hello : 'say hello target') {
    depends(init)
    echo(message : antProperty.'echo.msg')
}
setDefaultTarget(hello)
```

##### Apache Maven

Maven于2004年发布，它的目标是改进开发人员在使用Ant时面临的一些问题。Maven最初是为了简化Jakarta Turbine项目的构建的，他经历了从Maven到Maven3的发展，Maven作为后来者，继承了Ant的项目构建功能，同样采用了XML作为构建脚本的格式。Maven具有依赖管理和项目管理的功能，并提供了中央仓库，能够帮助我们自动下载库文件。

Maven的构建脚本的样式如下所示。

pom.xml

```xml
<project xmlns="http://maven.apache.……" xmlns:xsi="http://www.w3.……"
         xsi:schemaLocation="http://maven.apache……http://maven.apache……">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mycompany.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>Maven Quick Start Archetype</name>
    <url>http://maven.apache···</url>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

Maven相比Ant有以下优点。

（1）Ant是过程式的，开发这需要显示的指定每个目标，以及完成该目标所需要执行的任务。开发者需要重新编写每一个项目的过程，这样会产生大量的重复。而Maven是声明式的，项目的构建过程和过程中的各个阶段都由插件实现，开发者只需要声明项目的基本元素就可以了，这很大程度消除了重复。

（2）Ant本身是没有依赖管理的，续传配合Ivy来进行依赖管理，而Maven本身就提供了依赖管理。

（3）Maven使用的是约定而不是配置，它为工程提供了合理的默认行为，项目会知道去哪个目录寻找源码及构建运行时有哪些任务去执行，如果我们项目遵从默认值，那么只需要写几行XML配置脚本就可以了，而Ant是使用配置且没有默认行为的。

Maven也有一下缺点：

（1）Maven提供了默认的结构和生命周期，这些可能不适合我们的项目需求

（2）为Maven定制的扩展过于烦琐

（3）Maven的中央仓库比较混乱，当无法从中央仓库中得到需要的类库时，我们可以手工下载并复制到本地仓库中，也可以建立组织内部的仓库服务器。

（4）国内连接Maven的中央仓库比较慢，需要连接国内的Maven镜像仓库

（5）Maven缺乏相关的技术文档，不便于使用和理解

