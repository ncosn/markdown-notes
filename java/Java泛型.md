# Java 泛型

Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。

泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

## 泛型方法

### 注意细节

- 泛型方法可以定义在普通类中，也可以定义在泛型类中
- 当泛型方法被调用时，类型会确定
- public void ear(E e) {}，修饰符后没有<T,R...> eat方法不是泛型方法，而是使用了泛型（比如泛型类声明了参数）

你可以写一个泛型方法，该方法在调用时可以接收不同类型的参数。根据传递给泛型方法的参数类型，编译器适当地处理每一个方法调用。

下面是定义泛型方法的规则：

- 所有泛型方法声明都有一个类型参数声明部分（由尖括号分隔），该类型参数声明部分在方法返回类型之前（在下面例子中的 ）。
- 每一个类型参数声明部分包含一个或多个类型参数，参数间用逗号隔开。一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。
- 类型参数能被用来声明返回值类型，并且能作为泛型方法得到的实际参数类型的占位符。
- 泛型方法体的声明和其他方法一样。注意类型参数只能代表引用型类型，不能是原始类型（像 **int、double、char** 等）。

**java 中泛型标记符：**

- **E** - Element (在集合中使用，因为集合中存放的是元素)
- **T** - Type（Java 类）
- **K** - Key（键）
- **V** - Value（值）
- **N** - Number（数值类型）
- **？** - 表示不确定的 java 类型

```java
class DataHolder<T>{
    T item;
    
    public void setData(T t) {
    	this.item=t;
    }
    
    public T getData() {
    	return this.item;
    }
    
    /**
     * 泛型方法
     * @param e
     */
    public <E> void PrinterInfo(E e) {
    	System.out.println(e);
    }
}
```

有界的类型参数:
可能有时候，你会想限制那些被允许传递到一个类型参数的类型种类范围。例如，一个操作数字的方法可能只希望接受Number或者Number子类的实例。这就是有界类型参数的目的。
要声明一个有界的类型参数，首先列出类型参数的名称，后跟extends关键字，最后紧跟它的上界。

```java
// 比较三个值并返回最大值
public static <T extends Comparable<T>> T maximum(T x, T y, T z)
{                     
    T max = x; // 假设x是初始最大值
    if ( y.compareTo( max ) > 0 ){
        max = y; //y 更大
    }
    if ( z.compareTo( max ) > 0 ){
        max = z; // 现在 z 更大           
    }
    return max; // 返回最大对象
}
```

## 泛型类

### 注意细节

- 普通成员可以使用泛型
- 使用泛型的数组，不能初始化（因为数组在 new 不能确定 T 的类型，就无法在内存开空间）
- 静态方法中不能使用类的泛型
- 泛型类的类型，是在创建对象时确定的（因为创建对象时，需要指定确定类型）
- 如果在创建对象时，没有指定类型，默认为Object

泛型类的声明和非泛型类的声明类似，除了**在类名后面添加了类型参数声明部分**。

和泛型方法一样，泛型类的类型参数声明部分也包含一个或多个类型参数，参数间用逗号隔开。一个泛型参数，也被称为一个类型变量，是用于指定一个泛型类型名称的标识符。因为他们接受一个或多个参数，这些类被称为参数化的类或参数化的类型。

```java
public class Box<T> {
   
  private T t;
 
  public void add(T t) {
    this.t = t;
  }
 
  public T get() {
    return t;
  }
 
  public static void main(String[] args) {
    Box<Integer> integerBox = new Box<Integer>();
    Box<String> stringBox = new Box<String>();
 
    integerBox.add(new Integer(10));
    stringBox.add(new String("菜鸟教程"));
 
    System.out.printf("整型值为 :%d\n\n", integerBox.get());
    System.out.printf("字符串为 :%s\n", stringBox.get());
  }
}
```

## 类型通配符

1、类型通配符一般是使用 ? 代替具体的类型参数。例如 List<?> 在逻辑上是 List<String>,List<Integer> 等所有 List<具体类型实参> 的父类。

```java
import java.util.*;
 
public class GenericTest {
     
    public static void main(String[] args) {
        List<String> name = new ArrayList<String>();
        List<Integer> age = new ArrayList<Integer>();
        List<Number> number = new ArrayList<Number>();
        
        name.add("icon");
        age.add(18);
        number.add(314);
 
        getData(name);
        getData(age);
        getData(number);
       
   }
 
   public static void getData(List<?> data) {
      System.out.println("data :" + data.get(0));
   }
}
```

2、类型通配符上限通过形如List来定义，如此定义就是通配符泛型值接受Number及其下层子类类型。

```java
import java.util.*;
 
public class GenericTest {
     
    public static void main(String[] args) {
        List<String> name = new ArrayList<String>();
        List<Integer> age = new ArrayList<Integer>();
        List<Number> number = new ArrayList<Number>();
        
        name.add("icon");
        age.add(18);
        number.add(314);
 
        //getUperNumber(name);//1
        getUperNumber(age);//2
        getUperNumber(number);//3
       
   }
 
   public static void getData(List<?> data) {
      System.out.println("data :" + data.get(0));
   }
   
   public static void getUperNumber(List<? extends Number> data) {
          System.out.println("data :" + data.get(0));
       }
}
```

> 在 //1 处会出现错误，因为 getUperNumber() 方法中的参数已经限定了参数泛型上限为 Number，所以泛型为 String 是不在这个范围之内，所以会报错。


3、类型通配符下限通过形如 List<? super Number> 来定义，表示类型只能接受 Number 及其上层父类类型，如 Object 类型的实例。


## Java泛型接口

### 注意细节

- 接口中，静态成员也不能使用泛型（这个和泛型类规定一样）
- 泛型接口的类型，在**继承接口**或者**实现接口**时确定
- 没有指定类型，默认为Object

Java泛型接口的定义和Java泛型类基本相同，下面是一个例子：

```
复制代码//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```

此处有两点需要注意：

- 泛型接口未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中。例子如下：

```
/* 即：class DataHolder implements Generator<T>{
 * 如果不声明泛型，如：class DataHolder implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```

- 如果泛型接口传入类型参数时，实现该泛型接口的实现类，则所有使用泛型的地方都要替换成传入的实参类型。例子如下：

```
class DataHolder implements Generator<String> {
    @Override
    public String next() {
    	return null;
    }
}
```

从这个例子我们看到，实现类里面的所有T的地方都需要实现为String。

## Java泛型擦除及其相关内容

我们下面看一个例子：

```
Class<?> class1=new ArrayList<String>().getClass();
Class<?> class2=new ArrayList<Integer>().getClass();
System.out.println(class1);		//class java.util.ArrayList
System.out.println(class2);		//class java.util.ArrayList
System.out.println(class1.equals(class2));	//true
```

我们看输出发现，class1和class2居然是同一个类型ArrayList，在运行时我们传入的类型变量String和Integer都被丢掉了。Java语言泛型在设计的时候为了兼容原来的旧代码，Java的泛型机制使用了“擦除”机制。我们来看一个更彻底的例子：

```
class Table {}
class Room {}
class House<Q> {}
class Particle<POSITION, MOMENTUM> {}
//调用代码及输出
List<Table> tableList = new ArrayList<Table>();
Map<Room, Table> maps = new HashMap<Room, Table>();
House<Room> house = new House<Room>();
Particle<Long, Double> particle = new Particle<Long, Double>();
System.out.println(Arrays.toString(tableList.getClass().getTypeParameters()));
System.out.println(Arrays.toString(maps.getClass().getTypeParameters()));
System.out.println(Arrays.toString(house.getClass().getTypeParameters()));
System.out.println(Arrays.toString(particle.getClass().getTypeParameters()));
/** 
[E]
[K, V]
[Q]
[POSITION, MOMENTUM]
 */
```

上面的代码里，我们想在运行时获取类的类型参数，但是我们看到返回的都是“形参”。在运行期我们是获取不到任何已经声明的类型信息的。
注意：
编译器虽然会在编译过程中移除参数的类型信息，但是会保证类或方法内部参数类型的一致性。 
泛型参数将会被擦除到它的第一个边界（边界可以有多个，重用 extends 关键字，通过它能给与参数类型添加一个边界）。编译器事实上会把类型参数替换为它的第一个边界的类型。如果没有指明边界，那么类型参数将被擦除到Object。下面的例子中，可以把泛型参数T当作HasF类型来使用。

```
public interface HasF {
    void f();
}

public class Manipulator<T extends HasF> {
    T obj;
    public T getObj() {
        return obj;
    }
    public void setObj(T obj) {
        this.obj = obj;
    }
}
```

extend关键字后后面的类型信息决定了泛型参数能保留的信息。Java类型擦除只会擦除到HasF类型。

## 泛型可能出现的问题

### 1、类型转换的问题

如果我们想实现一个方法，想要将不确定的List集合转化为数组，那我们该怎么做？因为泛型的类型可擦除，我们无法直接从List中取得参数化类型T，所以只能从额外的参数中传递一个数组的泛型类型进去进行转换。

```
//必须传递Class<T> otherType作为参数类型
public static <T> T[] convert(List<T> list ,Class<T> otherType){
    T[] array = (T[]) Array.newInstance(otherType,list.size());
    return array;
}
public static void main(String[] args) {
    List<String> list = new ArrayList<String>(){{
        add("A");
    }};
 
    String[] result =convert(list,String.class);
}
```

当然，也可以通过反射手段来获取泛型类型。

```
Class clazz = list.getClass();
//getSuperclass()获得该类的父类

System.out.println(clazz.getSuperclass()); //class java.util.ArrayList
//getGenericSuperclass()获得带有泛型的父类
//Type是 Java 编程语言中所有类型的公共高级接口。它们包括原始类型、参数化类型、数组类型、类型变量和基本类型。

Type type = clazz.getGenericSuperclass();
System.out.println(type); //java.util.ArrayList<java.lang.String>

//ParameterizedType参数化类型，即泛型
ParameterizedType p = (ParameterizedType) type;

//getActualTypeArguments获取参数化类型的数组，泛型可能有多个
Class c = (Class) p.getActualTypeArguments()[0];
System.out.println(c); //class java.lang.String

String[] convert = convert(list, c);


//以上方法似乎有问题
public class MyClass {
	public List<String> stringList = ...;
}

Field field = MyClass.class.getField("stringList");
Type genericFieldType = field.getGenericType();
if(genericFieldType instanceof ParameterizedType){
	ParameterizedType aType = (ParameterizedType) genericFieldType;
	Type[] fieldArgTypes = aType.getActualTypeArguments();
	for(Type fieldArgType : fieldArgTypes){
		Class fieldArgClass = (Class) fieldArgType;
		System.out.println("fieldArgClass = " + fieldArgClass);
	}
}
```

Java反射：[https://zhuanlan.zhihu.com/p/522063575](https://zhuanlan.zhihu.com/p/522063575)

### 2、泛型与重载的矛盾

```
public static  void method(List<String> list){
}
public static  void method(List<Integer> list){
}

便已无法通过：
'method(List<Integer>)' clashes with 'method(List<String>)'; both methods have same erasure
```

我们已知上面的代码是无法通过编译的，因为List中的参数被擦除了，变成了原始类型的List。

## 泛型的最佳实践

经过之前的论述，大家已经知道了Java泛型的一些基础知识，以及在使用泛型的时候可能出现的问题。如果在使用泛型的时候可以遵循一些基本的原则，就能避免一些常见的问题。

      1. 在代码中避免泛型类和原始类型的混用。比如List< String>和List不应该共同使用。这样会产生一些编译器警告和潜在的运行时异常。当需要利用JDK 5之前开发的遗留代码，而不得不这么做时，也尽可能的隔离相关的代码。
      2. 在使用带通配符的泛型类的时候，尽可能的明确通配符所代表的一组类型的概念。
      3. 泛型类最好不要同数组一块使用。你只能创建new List<?>[10]这样的数组，无法创建new List[10]这样的。这限制了数组的使用能力，而且会带来很多费解的问题。因此，当需要类似数组的功能时候，使用集合类即可。
      4. 如果编译器给出的警告信息，在其他地方很多时候可以忽略(可能是格式带来的问题)，但是在泛型代码中还是尽量解决问题。

## 注意点：

泛型的工作方式是在编译阶段进行类型检查，而不是运行阶段。



