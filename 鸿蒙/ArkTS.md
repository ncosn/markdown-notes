# 目录

[TOC]



##　基本语法概述

在初步了解了ArkTS语言之后，我们以一个具体的示例来说明ArkTS的基本组成。如下图所示，当开发者点击按钮时，文本内容从“Hello World”变为“Hello ArkUI”。

ArkTS的基本组成

<img src="./ArkTS.assets/image-20240222170948974.png" alt="image-20240222170948974" style="zoom:50%;" />

>  **说明**：
>
> 自定义变量不能与基础通用属性/事件名重复

+ 装饰器：用于装饰类、结构、方法以及变量，并赋予其特殊的含义。如上述示例中`@Entry`、`@Component`和`@State`都是装饰器，[@Component](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/#自定义组件的基本结构)表示**自定义组件**，[@Entry](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/#自定义组件的基本结构)表示该自定义组件为**入口组件**，[@State](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md/)表示组件中的**状态变量**，状态变量变化会触发UI刷新。
+ [UI描述](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-declarative-ui-description.md/)：以声明式的方式来描述UI的结构，例如build()方法中的代码块。
+ [自定义组件](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/)：可复用的UI单元，可组合其他组件，如上述被@Component装饰的struct Hello。
+ 系统组件：ArkUI框架中默认内置的基础和容器组件，可直接被开发者调用，比如示例中的Column、Text、Divider、Button。

- 属性方法：组件可以通过链式调用配置多项属性，如fontSize()、width()、height()、backgroundColor()等。

- 事件方法：组件可以通过链式调用设置多个事件的响应逻辑，如跟随在Button后面的onClick()。

- 系统组件、属性方法、事件方法具体使用可参考[基于ArkTS的声明式开发范式](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-universal-events-click.md/)。



##  创建自定义组件

在ArkUI中，UI显示的内容均为组件，由框架直接提供的称为系统组件，由开发者定义的称为自定义组件。在进行 UI 界面开发时，通常不是简单的将系统组件进行组合使用，而是需要考虑代码可复用性、业务逻辑与UI分离，后续版本演进等因素。因此，将UI和部分业务逻辑封装成自定义组件是不可或缺的能力。

自定义组件具有以下特点：

- 可组合：允许开发者组合使用系统组件、及其属性和方法。
- 可重用：自定义组件可以被其他组件重用，并作为不同的实例在不同的父组件或容器中使用。
- 数据驱动UI更新：**通过状态变量的改变，来驱动UI的刷新**。

### 自定义组件的基本用法

以下示例展示了自定义组件的基本用法。

```ts
@Component
struct HelloComponent {
  @State message: string = 'Hello, World!';

  build() {
    // HelloComponent自定义组件组合系统组件Row和Text
    Row() {
      Text(this.message)
        .onClick(() => {
          // 状态变量message的改变驱动UI刷新，UI从'Hello, World!'刷新为'Hello, ArkUI!'
          this.message = 'Hello, ArkUI!';
        })
    }
  }
}
```

> **注意：**
>
> 如果在另外的文件中引用该自定义组件，需要使用export关键字导出，并在使用的页面import该自定义组件。

HelloComponent可以在其他自定义组件中的build()函数中多次创建，实现自定义组件的重用。

```ts
class HelloComponentParam {
  message: string = ""
}

@Entry
@Component
struct ParentComponent {
  param: HelloComponentParam = {
    message: 'Hello, World!'
  }

  build() {
    Column() {
      Text('ArkUI message')
      HelloComponent(this.param);
      Divider()
      HelloComponent(this.param);
    }
  }
}
```



### 自定义组件的基本结构

- struct：**自定义组件基于struct实现**，`struct + 自定义组件名 + {…}`的组合构成自定义组件，**不能有继承关系**。对于struct的实例化，可以省略new。

  > **说明：**
  >
  > 自定义组件名、类名、函数名不能和系统组件名相同。

- @Component：@Component装饰器仅能装饰struct关键字声明的数据结构。**struct被@Component装饰后具备组件化的能力**，需要实现build方法描述UI，一个struct只能被一个@Component装饰。

  > **说明：**
  >
  > 从API version 9开始，该装饰器支持在ArkTS卡片中使用。

  ```ts
  @Component
  struct MyComponent {
  }
  ```

- build()函数：build()函数用于定义自定义组件的声明式UI描述，**自定义组件必须定义build()函数**。

  ```ts
  @Component
  struct MyComponent {
    build() {
    }
  }
  ```

- @Entry：@Entry装饰的自定义组件将作为UI页面的入口。在单个UI页面中，最多可以使用@Entry装饰一个自定义组件。@Entry可以接受一个可选的[LocalStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md/)的参数。

  > **说明：**
  >
  > 从API version 9开始，该装饰器支持在ArkTS卡片中使用。
  >
  > 从API version 10开始，@Entry可以接受一个可选的[LocalStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md/)的参数或者一个可选的[EntryOptions](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/#entryOptions)参数。

  ```ts
  @Entry
  @Component
  struct MyComponent {
  }
  ```



### EntryOptions10+

命名路由跳转选项。

| 名称      | 类型                                                         | 必填 | 说明                         |
| :-------- | :----------------------------------------------------------- | :--- | :--------------------------- |
| routeName | string                                                       | 否   | 表示作为命名路由页面的名字。 |
| storage   | [LocalStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md/) | 否   | 页面级的UI状态存储。         |

```ts
@Entry({ routeName : 'myPage' })
@Component
struct MyComponent {
}
```

- @Reusable：@Reusable装饰的自定义组件具备可复用能力

  > **说明：**
  >
  > 从API version 10开始，该装饰器支持在ArkTS卡片中使用。

  ```ts
  @Reusable
  @Component
  struct MyComponent {
  }
  ```



### 成员函数/变量

自定义组件除了必须要实现build()函数外，**还可以实现其他成员函数**，成员函数具有以下约束：

- 自定义组件的**成员函数为私有的**，且**不建议声明成静态函数**。

自定义组件可以包含**成员变量**，成员变量具有以下约束：

- 自定义组件的**成员变量为私有的**，且**不建议声明成静态变量**。
- 自定义组件的成员变量本地初始化有些是可选的，有些是必选的。具体是否需要本地初始化，是否需要从父组件通过参数传递初始化子组件的成员变量，请参考[状态管理](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state-management-overview.md/)。



### 自定义组件的参数规定

从上文的示例中，我们已经了解到，可以在build方法或者[@Builder](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-builder.md/)装饰的函数里创建自定义组件，在创建自定义组件的过程中，根据装饰器的规则来初始化自定义组件的参数。

```ts
@Component
struct MyComponent {
  private countDownFrom: number = 0;
  private color: Color = Color.Blue;

  build() {
  }
}

@Entry
@Component
struct ParentComponent {
  private someColor: Color = Color.Pink;

  build() {
    Column() {
      // 创建MyComponent实例，并将创建MyComponent成员变量countDownFrom初始化为10，将成员变量color初始化为this.someColor
      MyComponent({ countDownFrom: 10, color: this.someColor })
    }
  }
}
```



### build()函数

所有声明在build()函数的语言，我们统称为UI描述，UI描述需要遵循一下规则：

+ `@Entry`装饰的自定义组件，其build()函数下的**根节点唯一且必要，且必须为容器组件**，其中**ForEach禁止作为根节点**。`@Component`装饰的自定义组件，其build()函数下的**根节点唯一且必要，可以为非容器组件**，其中**ForEach禁止作为根节点**。

  ```ts
  @Entry
  @Component
  struct MyComponent {
    build() {
      // 根节点唯一且必要，必须为容器组件
      Row() {
        ChildComponent() 
      }
    }
  }
  
  @Component
  struct ChildComponent {
    build() {
      // 根节点唯一且必要，可为非容器组件
      Image('test.jpg')
    }
  }
  ```

+ 不允许声明本地变量，反例如下。

  ```ts
  build() {
    // 反例：不允许声明本地变量
    let a: number = 1;
  }
  ```

+ 不允许在UI描述里直接只用console.info，但允许在方法或者函数里使用，反例如下。

  ```ts
  build() {
    // 反例：不允许console.info
    console.info('print debug log');
  }
  ```

+ 不允许创建本地的作用域，反例如下。

  ```ts
  build() {
    // 反例：不允许本地作用域
    {
      ...
    }
  }
  ```

+ **不允许调用没有用@Builder装饰的方法**，**允许系统组件的参数是TS方法的返回值**。

  ```ts
  @Component
  struct ParentComponent {
    doSomeCalculations() {
    }
  
    calcTextValue(): string {
      return 'Hello World';
    }
  
    @Builder doSomeRender() {
      Text(`Hello World`)
    }
  
    build() {
      Column() {
        // 反例：不能调用没有用@Builder装饰的方法
        this.doSomeCalculations();
        // 正例：可以调用
        this.doSomeRender();
        // 正例：参数可以为调用TS方法的返回值
        Text(this.calcTextValue())
      }
    }
  }
  ```

+ 不允许switch语法，如果需要使用条件判断，请使用if。反例如下。

  ```ts
  build() {
    Column() {
      // 反例：不允许使用switch语法
      switch (expression) {
        case 1:
          Text('...')
          break;
        case 2:
          Image('...')
          break;
        default:
          Text('...')
          break;
      }
    }
  }
  ```

+ 不允许使用表达式，反例如下。

  ```ts
  build() {
    Column() {
      // 反例：不允许使用表达式
      (this.aVar > 10) ? Text('...') : Image('...')
    }
  }
  ```

+ 不允许直接改变状态变量，反例如下。

  ```ts
  @Component
  struct CompA {
    @State col1: Color = Color.Yellow;
    @State col2: Color = Color.Green;
    @State count: number = 1;
    build() {
      Column() {
        // 应避免直接在Text组件内改变count的值
        Text(`${this.count++}`)
          .width(50)
          .height(50)
          .fontColor(this.col1)
          .onClick(() => {
            this.col2 = Color.Red;
          })
        Button("change col1").onClick(() =>{
          this.col1 = Color.Pink;
        })
      }
      .backgroundColor(this.col2)
    }
  }
  ```

  在ArkUI状态管理中，状态驱动UI更新。

  `UI = f(State)`

  所以，不能在自定义组件的build()或@Builder方法里直接改变状态变量，这可能会造成循环渲染的风险。Text(‘${this.count++}’)在全量更新或最小化更新会产生不同的影响：

  - 全量更新： ArkUI可能会陷入一个无限的重渲染的循环里，因为Text组件的每一次渲染都会改变应用的状态，就会再引起下一轮渲染的开启。 当 this.col2 更改时，都会执行整个build构建函数，因此，Text(`${this.count++}`)绑定的文本也会更改，每次重新渲染Text(`${this.count++}`)，又会使this.count状态变量更新，导致新一轮的build执行，从而陷入无限循环。
  - 最小化更新： 当 this.col2 更改时，只有Column组件会更新，Text组件不会更改。 只当 this.col1 更改时，会去更新整个Text组件，其所有属性函数都会执行，所以会看到Text(`${this.count++}`)自增。因为目前UI以组件为单位进行更新，如果组件上某一个属性发生改变，会更新整体的组件。所以整体的更新链路是：this.col1 = Color.Pink -> Text组件整体更新->this.count++ ->Text组件整体更新。值得注意的是，这种写法在初次渲染时会导致Text组件渲染两次，从而对性能产生影响。

  build函数中更改应用状态的行为可能会比上面的示例更加隐蔽，比如：

  - 在@Builder，@Extend或@Styles方法内改变状态变量 。

  - 在计算参数时调用函数中改变应用状态变量，例如 Text(‘${this.calcLabel()}’)。

  - 对当前数组做出修改，sort()改变了数组this.arr，随后的filter方法会返回一个新的数组。

    ```ts
    // 反例
    @State arr : Array<...> = [ ... ];
    ForEach(this.arr.sort().filter(...), 
      item => { 
      ...
    })
    // 正确的执行方式为：filter返回一个新数组，后面的sort方法才不会改变原数组this.arr
    ForEach(this.arr.filter(...).sort(), 
      item => { 
      ...
    })
    
    ```

    

### 自定义组件通用样式

自定义组件通过“.”链式调用的形式设置通用样式。

```ts
@Component
struct MyComponent2 {
  build() {
    Button(`Hello World`)
  }
}

@Entry
@Component
struct MyComponent {
  build() {
    Row() {
      MyComponent2()
        .width(200)
        .height(300)
        .backgroundColor(Color.Red)
    }
  }
}
```

> **说明：**
>
> ArkUI给自定义组件设置样式时，相当于给MyComponent2套了一个不可见的容器组件，而这些样式是设置在容器组件上的，而非直接设置给MyComponent2的Button组件。通过渲染结果我们可以很清楚的看到，背景颜色红色并没有直接生效在Button上，而是生效在Button所处的开发者不可见的容器组件上。



## 页面和自定义组件生命周期

在开始之前，我们先明确自定义组件和页面的关系：

+ 自定义组件：@Component装饰的UI单元，可以组合多个系统组件实现UI的复用，可以调用组件的生命周期。
+ 页面：即应用的UI页面。可以由一个或者多个自定义组件组成，@Entry装饰的自定义组件为页面的入口组件，即页面的根节点，一个页面有且仅能有一个@Entry。**只有被@Entry装饰的组件才可以调用页面的生命周期**

**页面生命周期**，即被@Entry装饰的组件生命周期，提供以下生命周期接口：

+ `onPageShow`：页面每次显示时触发一次，包括路由过程、应用进入前台等场景。
+ `onPageHide`：页面每次隐藏时触发一次，包括路由过程、应用今不如后台等场景。
+ `onBackPress`：当用户点击返回按钮时触发。

**组件生命周期**，即一般用@Component装饰的自定义组件的生命周期，提供以下生命周期接口：

+ `aboutToAppear`：组件即将出现时回调该接口，具体时机为在创建自定义组件的新实例后，在执行其build()函数之前执行。
+ `aboutToDisappear`：aboutToDisappear函数在自定义组件析构销毁之前执行。不允许在aboutToDisappear函数中改变状态变量，特别是@Link变量的修改可能会导致应用程序行为不稳定。

生命周期流程如下图所示，下图展示的是被@Entry装饰的组件（页面）生命周期。

![image-20240228135144913](./ArkTS.assets/image-20240228135144913.png)

根据上面的流程，我们从自定义组件的初始创建、重新渲染和删除来详细解释。



### 自定义组件的创建和渲染流程

1. 自定义组件的创建：自定义组件的实例由ArkUI框架创建。

2. 初始化自定义组件的成员变量：通过本地默认值或者构造方法传递参数来初始化自定义组件的成员变量，初始化顺序为成员变量的定义顺序。

3. 如果开发者自定义了aboutToAppear，则执行aboutToAppear方法。

4. 在首次渲染的时候，执行build方法渲染系统组件，如果子组件为自定义组件，则创建自定义组件的实例。在执行build()函数的过程中，框架会观察每个状态变量的读取状态，将保存两个map：

   1. 状态变量 -> UI组件（包括ForEach和if）、

   2. UI组件 -> 此组件的跟新函数，即一个lambda方法，作为build()函数的子集，创建对应的UI组件并执行其属性方法，示例如下。

      ```ts
      build() {
        ...
        this.observeComponentCreation(() => {
          Button.create();
        })
      
        this.observeComponentCreation(() => {
          Text.create();
        })
        ...
      }
      ```

当应用在后台启动时，此时应用进程并没有销毁，所以仅需要执行onPageShow。

### 自定义组件重新渲染

当事件句柄被触发（比如设置了点击事件，即触发点击事件）改变了状态变量时，或者LocalStorage/AppStorage中的属性更改，并导致绑定的状态变量更改其值时：

1. 框架观察到了变化，将启动重新渲染。
2. 根据框架持有的两个map（自定义组件的创建和渲染流程中第4步），框架可以知道该状态变量管理了哪些UI组件，以及这些UI组件对应的更新函数。执行这些UI组件的更新函数，实现最小化更新。



### 自定义组件的删除

如果if组件的分支改变，或者ForEach循环渲染中数组的个数改变，组件将被删除：

1. 在删除组件之前，将调用其aboutToDisappear生命周期函数，标记着该节点将要被销毁。ArkUI的节点删除机制是：后端节点直接从组件树上摘下，后端节点被销毁，对前端节点解引用，前端节点已经没有引用时，将被JS虚拟机垃圾回收。
2. 自定义组件和它的变量将被删除，如果其有同步的变量，比如[@Link](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md)、[@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)、[@StorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storagelink)，将从[同步源](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state-management-overview.md#基本概念)上取消注册。

不建议在生命周期aboutToDisappear内使用async await，如果在生命周期的aboutToDisappear使用异步操作（Promise或者回调方法），自定义组件将被保留在Promise的闭包中，直到回调方法被执行完，这个行为阻止了自定义组件的垃圾回收。

以下示例展示了生命周期的调用时机：

```ts
// Index.ets
import router from '@ohos.router';

@Entry
@Component
struct MyComponent {
  @State showChild: boolean = true;
  @State btnColor:string = "#FF007DFF"

  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageShow() {
    console.info('Index onPageShow');
  }
  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onPageHide() {
    console.info('Index onPageHide');
  }

  // 只有被@Entry装饰的组件才可以调用页面的生命周期
  onBackPress() {
    console.info('Index onBackPress');
    this.btnColor ="#FFEE0606"
    return true // 返回true表示页面自己处理返回逻辑，不进行页面路由；返回false表示使用默认的路由返回逻辑，不设置返回值按照false处理
  }

  // 组件生命周期
  aboutToAppear() {
    console.info('MyComponent aboutToAppear');
  }

  // 组件生命周期
  aboutToDisappear() {
    console.info('MyComponent aboutToDisappear');
  }

  build() {
    Column() {
      // this.showChild为true，创建Child子组件，执行Child aboutToAppear
      if (this.showChild) {
        Child()
      }
      // this.showChild为false，删除Child子组件，执行Child aboutToDisappear
      Button('delete Child')
      .margin(20)
      .backgroundColor(this.btnColor)
      .onClick(() => {
        this.showChild = false;
      })
      // push到page页面，执行onPageHide
      Button('push to next page')
        .onClick(() => {
          router.pushUrl({ url: 'pages/page' });
        })
    }

  }
}

@Component
struct Child {
  @State title: string = 'Hello World';
  // 组件生命周期
  aboutToDisappear() {
    console.info('[lifeCycle] Child aboutToDisappear')
  }
  // 组件生命周期
  aboutToAppear() {
    console.info('[lifeCycle] Child aboutToAppear')
  }

  build() {
    Text(this.title).fontSize(50).margin(20).onClick(() => {
      this.title = 'Hello ArkUI';
    })
  }
}
ts
// page.ets
@Entry
@Component
struct page {
  @State textColor: Color = Color.Black;
  @State num: number = 0

  onPageShow() {
    this.num = 5
  }

  onPageHide() {
    console.log("page onPageHide");
  }

  onBackPress() { // 不设置返回值按照false处理
    this.textColor = Color.Grey
    this.num = 0
  }

  aboutToAppear() {
    this.textColor = Color.Blue
  }

  build() {
    Column() {
      Text(`num 的值为：${this.num}`)
        .fontSize(30)
        .fontWeight(FontWeight.Bold)
        .fontColor(this.textColor)
        .margin(20)
        .onClick(() => {
          this.num += 5
        })
    }
    .width('100%')
  }
}
```

以上示例中，Index页面包含两个自定义组件，一个是被@Entry装饰的MyComponent，也是页面的入口组件，即页面的根节点；一个是Child，是MyComponent的子组件。只有@Entry装饰的节点才可以使页面级别的生命周期方法生效，因此在MyComponent中声明当前Index页面的页面生命周期函数（onPageShow / onPageHide / onBackPress）。MyComponent和其子组件Child分别声明了各自的组件级别生命周期函数（aboutToAppear / aboutToDisappear）。

- 应用冷启动的初始化流程为：MyComponent aboutToAppear --> MyComponent build --> Child aboutToAppear --> Child build --> Child build执行完毕 --> MyComponent build执行完毕 --> Index onPageShow。
- 点击“delete Child”，if绑定的this.showChild变成false，删除Child组件，会执行Child aboutToDisappear方法。
- 点击“push to next page”，调用router.pushUrl接口，跳转到另外一个页面，当前Index页面隐藏，执行页面生命周期Index onPageHide。此处调用的是router.pushUrl接口，Index页面被隐藏，并没有销毁，所以只调用onPageHide。跳转到新页面后，执行初始化新页面的生命周期的流程。
- 如果调用的是router.replaceUrl，则当前Index页面被销毁，执行的生命周期流程将变为：Index onPageHide --> MyComponent aboutToDisappear --> Child aboutToDisappear。上文已经提到，组件的销毁是从组件树上直接摘下子树，所以先调用父组件的aboutToDisappear，再调用子组件的aboutToDisappear，然后执行初始化新页面的生命周期流程。
- 点击返回按钮，触发页面生命周期Index onBackPress，且触发返回一个页面后会导致当前Index页面被销毁。
- 最小化应用或者应用进入后台，触发Index onPageHide。当前Index页面没有被销毁，所以并不会执行组件的aboutToDisappear。应用回到前台，执行Index onPageShow。
- 退出应用，执行Index onPageHide --> MyComponent aboutToDisappear --> Child aboutToDisappear。





## @Builder装饰器：自定义构建函数

前面章节介绍了如何创建一个自定义组件。该自定义组件内部UI结构固定，仅与使用方进行数据传递。ArkUI还提供了一种更轻量的UI元素复用机制@Builder，@Builder所装饰的函数遵循build()函数语法规则，开发者可以将重复使用的UI元素抽象成一个方法，在build方法里调用。

为了简化语言，我们将@Builder装饰的函数也称为“自定义构建函数”。

> 说明：
>
> 从API version 9开始，该装饰器支持在ArkTS卡片中使用。

### 装饰器使用说明

#### 自定义组件内自定义构建函数

定义的语法：

```ts
@Builder MyBuilderFunction() {...}
```

使用方法：

```ts
this.MyBuilderFunction()
```

- 允许在自定义组件内定义**一个或多个@Builder方法**，该方法被认为是**该组件的私有、特殊类型的成员函数**。
- 自定义构建函数**可以在所属组件的build方法和其他自定义构建函数中调用，但不允许在组件外调用**。
- 在自定义函数体中，**this指代当前所属组件**，**组件的状态变量可以在自定义构建函数内访问**。建议通过this访问自定义组件的状态变量而不是参数传递。

#### 全局自定义构建函数

定义的语法：

```ts
@Builder function MyGlobalBuilderFunction() { ... }
```

使用方法：

```ts
MyGlobalBuilderFunction()
```

- **全局的自定义构建函数可以被整个应用获取，不允许使用this和bind方法**。
- **如果不涉及组件状态变化，建议使用全局的自定义构建方法**。



### 参数传递规则

自定义构建函数的参数传递有[按值传递](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-builder.md#按值传递参数)和[按引用传递](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-builder.md#按引用传递参数)两种，均需遵守以下规则：

- 参数的类型必须与参数声明的类型一致，不允许undefined、null和返回undefined、null的表达式。
- 在@Builder修饰的函数内部，**不允许改变参数值**。
- @Builder内UI语法遵循[UI语法规则](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md#build函数)。
- 只有**传入一个参数，且参数需要直接传入对象字面量**才会**按引用传递该参数**，其余传递方式均为按值传递。

#### 按引用参数传递

按引用传递参数时，传递的参数可为状态变量，且状态变量的改变会引起@Builder方法内的UI刷新。

```ts
class Tmp {
  paramA1: string = ''
  paramB1: string = ''
}

@Builder function overBuilder(params : Tmp) {...}
```

```ts
class Tmp {
  paramA1: string = ''
}

@Builder function overBuilder(params: Tmp) {
  Row() {
    Text(`UseStateVarByReference: ${params.paramA1} `)
  }
}
@Entry
@Component
struct Parent {
  @State label: string = 'Hello';
  build() {
    Column() {
      // 在Parent组件中调用overBuilder的时候，将this.label引用传递给overBuilder
      overBuilder({ paramA1: this.label })
      Button('Click me').onClick(() => {
        // 点击“Click me”后，UI从“Hello”刷新为“ArkUI”
        this.label = 'ArkUI';
      })
    }
  }
}
```

**按引用传递参数时，如果在@Builder方法内调用自定义组件，ArkUI提供$$作为按引用传递参数的范式。**

```ts
class Tmp {
  paramA1: string = ''
  paramB1: string = ''
}

@Builder function overBuilder($$ : Tmp) {...}
```

```ts
class Tmp {
  paramA1: string = ''
}

@Builder function overBuilder($$: Tmp) {
  Row() {
    Column() {
      Text(`overBuilder===${$$.paramA1}`)
      HelloComponent({message: $$.paramA1})
    }
  }
}

@Component
struct HelloComponent {
  @Link message: string;

  build() {
    Row() {
      Text(`HelloComponent===${this.message}`)
    }
  }
}

@Entry
@Component
struct Parent {
  @State label: string = 'Hello';
  build() {
    Column() {
      // Pass the this.label reference to the overBuilder component when the overBuilder component is called in the Parent component.
      overBuilder({paramA1: this.label})
      Button('Click me').onClick(() => {
        // After Click me is clicked, the UI text changes from Hello to ArkUI.
        this.label = 'ArkUI';
      })
    }
  }
}
```

#### 按值传递参数

调用@Builder装饰的函数默认按值传递。当传递的参数为状态变量时，状态变量的改变不会引起@Builder方法内的UI刷新。所以当使用状态变量的时候，推荐使用[按引用传递](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-builder.md#按引用传递参数)。

```ts
@Builder function overBuilder(paramA1: string) {
  Row() {
    Text(`UseStateVarByValue: ${paramA1} `)
  }
}
@Entry
@Component
struct Parent {
  @State label: string = 'Hello';
  build() {
    Column() {
      overBuilder(this.label)
    }
  }
}
```



## @BuilderParam装饰器：引用@Builder函数

当开发者创建了自定义组件，并想对该组件添加特定功能时，例如在自定义组件中添加一个点击跳转操作。若直接在组件内嵌入事件方法，将会导致所有引入该自定义组件的地方均增加了该功能。为解决此问题，ArkUI引入了@BuilderParam装饰器，@BuilderParam用来装饰指向@Builder方法的变量，开发者可在初始化自定义组件时对此属性进行赋值，为自定义组件增加特定的功能。该装饰器用于声明任意UI描述的一个元素，类似slot占位符。

### 装饰器使用说明

#### 初始化@BuilderParam装饰的方法

@BuilderParam装饰的方法只能被自定义构建函数（@Builder装饰的方法）初始化。

+ 使用所属自定义组件的自定义构建函数或者全局的自定义构建函数，在本地初始化@BuilderParam。

  ```ts
  @Builder function overBuilder() {}
  
  @Component
  struct Child {
    @Builder doNothingBuilder() {};
  
    // 使用自定义组件的自定义构建函数初始化@BuilderParam
    @BuilderParam customBuilderParam: () => void = this.doNothingBuilder;
    // 使用全局自定义构建函数初始化@BuilderParam
    @BuilderParam customOverBuilderParam: () => void = overBuilder;
    build(){}
  }
  ```

+ 用父组件自定义构建函数初始化子组件@BuilderParam装饰的方法。

  ```ts
  @Component
  struct Child {
    @Builder customBuilder() {}
    // 使用父组件@Builder装饰的方法初始化子组件@BuilderParam
    @BuilderParam customBuilderParam: () => void = this.customBuilder;
  
    build() {
      Column() {
        this.customBuilderParam()
      }
    }
  }
  
  @Entry
  @Component
  struct Parent {
    @Builder componentBuilder() {
      Text(`Parent builder `)
    }
  
    build() {
      Column() {
        Child({ customBuilderParam: this.componentBuilder })
      }
    }
  }
  ```

+ **需注意this指向正确**。

  以下示例中，Parent组件在调用this.componentBuilder()时，this指向其所属组件，即“Parent”。@Builder componentBuilder()传给子组件@BuilderParam customBuilderParam，在Child组件中调用this.customBuilderParam()时，this指向在Child的label，即“Child”。

  ```ts
  @Component
  struct Child {
    label: string = `Child`
    @Builder customBuilder() {}
    @Builder customChangeThisBuilder() {}
    @BuilderParam customBuilderParam: () => void = this.customBuilder;
    @BuilderParam customChangeThisBuilderParam: () => void = this.customChangeThisBuilder;
  
    build() {
      Column() {
        this.customBuilderParam()
        this.customChangeThisBuilderParam()
      }
    }
  }
  
  @Entry
  @Component
  struct Parent {
    label: string = `Parent`
  
    @Builder componentBuilder() {
      Text(`${this.label}`)
    }
  
    build() {
      Column() {
        this.componentBuilder()
        Child({ customBuilderParam: this.componentBuilder, customChangeThisBuilderParam: ():void=>{this.componentBuilder()} })
      }
    }
  }
  ```

  

### 使用场景

#### 参数初始化组件

@BuilderParam装饰的方法可以是有参数和无参数的两种形式，需与指向的@Builder方法类型匹配。@BuilderParam装饰的方法类型需要和@Builder方法类型一致。

```ts
class Tmp{
  label:string = ''
}
@Builder function overBuilder($$ : Tmp) {
  Text($$.label)
    .width(400)
    .height(50)
    .backgroundColor(Color.Green)
}

@Component
struct Child {
  label: string = 'Child'
  @Builder customBuilder() {}
  // 无参数类型，指向的componentBuilder也是无参数类型
  @BuilderParam customBuilderParam: () => void = this.customBuilder;
  // 有参数类型，指向的overBuilder也是有参数类型的方法
  @BuilderParam customOverBuilderParam: ($$ : Tmp) => void = overBuilder;

  build() {
    Column() {
      this.customBuilderParam()
      this.customOverBuilderParam({label: 'global Builder label' } )
    }
  }
}

@Entry
@Component
struct Parent {
  label: string = 'Parent'

  @Builder componentBuilder() {
    Text(`${this.label}`)
  }

  build() {
    Column() {
      this.componentBuilder()
      Child({ customBuilderParam: this.componentBuilder, customOverBuilderParam: GlobalBuilder1 })
    }
  }
}
```

#### 尾随闭包初始化组件

在自定义组件中使用@BuilderParam装饰的属性时也可通过尾随闭包进行初始化。在初始化自定义组件时，组件后紧跟一个大括号“{}”形成尾随闭包场景。

> **说明：**
>
> - 此场景下自定义组件内有且仅有一个使用@BuilderParam装饰的属性。
> - 此场景下自定义组件不支持使用通用属性。

开发者可以将尾随闭包内的内容看做@Builder装饰的函数传给@BuilderParam。示例如下：

```ts
// xxx.ets
@Component
struct CustomContainer {
  @Prop header: string = '';
  @Builder closerBuilder(){}
  // 使用父组件的尾随闭包{}(@Builder装饰的方法)初始化子组件@BuilderParam
  @BuilderParam closer: () => void = this.closerBuilder

  build() {
    Column() {
      Text(this.header)
        .fontSize(30)
      this.closer()
    }
  }
}

@Builder function specificParam(label1: string, label2: string) {
  Column() {
    Text(label1)
      .fontSize(30)
    Text(label2)
      .fontSize(30)
  }
}

@Entry
@Component
struct CustomContainerUser {
  @State text: string = 'header';

  build() {
    Column() {
      // 创建CustomContainer，在创建CustomContainer时，通过其后紧跟一个大括号“{}”形成尾随闭包
      // 作为传递给子组件CustomContainer @BuilderParam closer: () => void的参数
      CustomContainer({ header: this.text }) {
        Column() {
          specificParam('testA', 'testB')
        }.backgroundColor(Color.Yellow)
        .onClick(() => {
          this.text = 'changeHeader';
        })
      }
    }
  }
}
```



## @Styles装饰器：定义组件重用样式

如果每个组件的样式都需要单独设置，在开发过程中会出现大量代码在进行重复样式设置，虽然可以复制粘贴，单位了代码简洁性和后续方便维护，我们推出了可以提炼公共样式进行服用的装饰器@Styles。

@Styles装饰器可以将多条样式设置提炼成一个方法，直接在组件声明的位置调用。通过@Styles装饰器可以快速定义并服用自定义样式。用于快速定义并服用自定义样式。

### 装饰器使用说明

+ 当前@Styles仅支持[通用属性](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-universal-attributes-size.md)和[通用事件](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-universal-events-click.md)。

+ @Styles方法不支持参数，反例如下。

  ```ts
  // 反例： @Styles不支持参数
  @Styles function globalFancy (value: number) {
    .width(value)
  }
  ```

+ @Styles可以定义在组件内或全局，在全局定义时需在方法名前面添加function关键字，组件内定义时则不需要添加function关键字。

> 只能在当前文件内使用，不支持export

```ts
// 全局
@Styles function functionName() { ... }

// 在组件内
@Component
struct FancyUse {
  @Styles fancy() {
    .height(100)
  }
}
```

+ 定义在组件内的@Styles可以通过this访问组件的常量和状态变量，并可以在@Styles里通过事件来改变状态变量的值，示例如下：

  ```ts
  @Component
  struct FancyUse {
    @State heightValue: number = 100
    @Styles fancy() {
      .height(this.heightValue)
      .backgroundColor(Color.Yellow)
      .onClick(() => {
        this.heightValue = 200
      })
    }
  }
  ```

+ 组件内@Styles的优先级高于全局@Styles。框架优先找当前组件内的@Styles，如果找不到，则会全局查找。



### 使用场景

以下示例中演示了组件内@Styles和全局@Styles的用法。

```ts
// 定义在全局的@Styles封装的样式
@Styles function globalFancy  () {
  .width(150)
  .height(100)
  .backgroundColor(Color.Pink)
}

@Entry
@Component
struct FancyUse {
  @State heightValue: number = 100
  // 定义在组件内的@Styles封装的样式
  @Styles fancy() {
    .width(200)
    .height(this.heightValue)
    .backgroundColor(Color.Yellow)
    .onClick(() => {
      this.heightValue = 200
    })
  }

  build() {
    Column({ space: 10 }) {
      // 使用全局的@Styles封装的样式
      Text('FancyA')
        .globalFancy()
        .fontSize(30)
      // 使用组件内的@Styles封装的样式
      Text('FancyB')
        .fancy()
        .fontSize(30)
    }
  }
}
```



## @Extend装饰器：定义扩展组件样式

在前文的示例中，可以使用@Styles用于样式的扩展，在@Styles的基础上，我们提供了@Extend，用于扩展原生组件样式。

### 装饰器使用说明

#### 语法

```ts
@Extend(UIComponentName) function functionName { ... }
```

### 使用规则

- 和@Styles不同，@Extend仅支持在全局定义，不支持在组件内部定义。

> **说明：** 只能在当前文件内使用，不支持export

+ 和@Styles不同，@Extend支持封装指定的组件的私有属性和私有事件，以及预定义相同组件的@Extend的方法。

  ```ts
  // @Extend(Text)可以支持Text的私有属性fontColor
  @Extend(Text) function fancy () {
    .fontColor(Color.Red)
  }
  // superFancyText可以调用预定义的fancy
  @Extend(Text) function superFancyText(size:number) {
      .fontSize(size)
      .fancy()
  }
  ```

+ 和@Styles不同，@Extend装饰的方法支持参数，开发者可以在调用时传递参数，调用遵循TS方法传值调用。

  ```ts
  // xxx.ets
  @Extend(Text) function fancy (fontSize: number) {
    .fontColor(Color.Red)
    .fontSize(fontSize)
  }
  
  @Entry
  @Component
  struct FancyUse {
    build() {
      Row({ space: 10 }) {
        Text('Fancy')
          .fancy(16)
        Text('Fancy')
          .fancy(24)
      }
    }
  }
  ```

+ @Extend装饰的方法的参数可以为function，作为Event事件的句柄。

  ```ts
  @Extend(Text) function makeMeClick(onClick: () => void) {
    .backgroundColor(Color.Blue)
    .onClick(onClick)
  }
  
  @Entry
  @Component
  struct FancyUse {
    @State label: string = 'Hello World';
  
    onClickHandler() {
      this.label = 'Hello ArkUI';
    }
  
    build() {
      Row({ space: 10 }) {
        Text(`${this.label}`)
          .makeMeClick(() => {this.onClickHandler()})
      }
    }
  }
  ```

+ @Extend的参数可以为[状态变量](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state-management-overview.md)，当状态变量改变时，UI可以正常的被刷新渲染。

  ```ts
  @Extend(Text) function fancy (fontSize: number) {
    .fontColor(Color.Red)
    .fontSize(fontSize)
  }
  
  @Entry
  @Component
  struct FancyUse {
    @State fontSizeValue: number = 20
    build() {
      Row({ space: 10 }) {
        Text('Fancy')
          .fancy(this.fontSizeValue)
          .onClick(() => {
            this.fontSizeValue = 30
          })
      }
    }
  }
  ```



### 使用场景

以下示例声明了3个Text组件，每个Text组件均设置了fontStyle、fontWeight和backgroundColor样式。

```ts
@Entry
@Component
struct FancyUse {
  @State label: string = 'Hello World'

  build() {
    Row({ space: 10 }) {
      Text(`${this.label}`)
        .fontStyle(FontStyle.Italic)
        .fontWeight(100)
        .backgroundColor(Color.Blue)
      Text(`${this.label}`)
        .fontStyle(FontStyle.Italic)
        .fontWeight(200)
        .backgroundColor(Color.Pink)
      Text(`${this.label}`)
        .fontStyle(FontStyle.Italic)
        .fontWeight(300)
        .backgroundColor(Color.Orange)
    }.margin('20%')
  }
}
```

@Extend将样式组合复用，示例如下。

```ts
@Extend(Text) function fancyText(weightValue: number, color: Color) {
  .fontStyle(FontStyle.Italic)
  .fontWeight(weightValue)
  .backgroundColor(color)
}
```

通过@Extend组合样式后，使得代码更加简洁，增强可读性。

```ts
@Entry
@Component
struct FancyUse {
  @State label: string = 'Hello World'

  build() {
    Row({ space: 10 }) {
      Text(`${this.label}`)
        .fancyText(100, Color.Blue)
      Text(`${this.label}`)
        .fancyText(200, Color.Pink)
      Text(`${this.label}`)
        .fancyText(300, Color.Orange)
    }.margin('20%')
  }
}
```



## stateStyles：多态样式

@Styles和@Extend仅仅应用于静态页面的样式服用，stateStyles可以依据组件的内部状态的不同，快速设置不同样式。这就是我们本章要介绍的内容stateStyles（又称为：多态样式）。

### 概述

stateStyles是属性方法，可以根据UI内部状态来设置样式，类似于css伪类，但语法不同。ArkUI提供以下五种状态：

- `focused`：获焦态。
- `normal`：正常态。
- `pressed`：按压态。
- `disabled`：不可用态。
- `selected10+`：选中态。

### 使用场景

#### 基础场景

下面的示例展示了stateStyles最基本的使用场景。Button1处于第一个组件，Button2处于第二个组件。按压时显示为pressed态指定的黑色。使用Tab键走焦，先是Button1获焦并显示为focus态指定的粉色。当Button2获焦的时候，Button2显示为focus态指定的粉色，Button1失焦显示normal态指定的红色。

```ts
@Entry
@Component
struct StateStylesSample {
  build() {
    Column() {
      Button('Button1')
        .stateStyles({
          focused: {
            .backgroundColor(Color.Pink)
          },
          pressed: {
            .backgroundColor(Color.Black)
          },
          normal: {
            .backgroundColor(Color.Red)
          }
        })
        .margin(20)
      Button('Button2')
        .stateStyles({
          focused: {
            .backgroundColor(Color.Pink)
          },
          pressed: {
            .backgroundColor(Color.Black)
          },
          normal: {
            .backgroundColor(Color.Red)
          }
        })
    }.margin('30%')
  }
}
```



#### @Styles和stateStyles联合使用

以下示例通过@Styles指定stateStyles的不同状态。

```ts
@Entry
@Component
struct MyComponent {
  @Styles normalStyle() {
    .backgroundColor(Color.Gray)
  }

  @Styles pressedStyle() {
    .backgroundColor(Color.Red)
  }

  build() {
    Column() {
      Text('Text1')
        .fontSize(50)
        .fontColor(Color.White)
        .stateStyles({
          normal: this.normalStyle,
          pressed: this.pressedStyle,
        })
    }
  }
}
```

#### 在stateStyles里使用常规变量和状态变量

stateStyles可以通过this绑定组件内的常规变量和状态变量。

```ts
@Entry
@Component
struct CompWithInlineStateStyles {
  @State focusedColor: Color = Color.Red;
  normalColor: Color = Color.Green

  build() {
    Column() {
      Button('clickMe').height(100).width(100)
        .stateStyles({
          normal: {
            .backgroundColor(this.normalColor)
          },
          focused: {
            .backgroundColor(this.focusedColor)
          }
        })
        .onClick(() => {
          this.focusedColor = Color.Pink
        })
        .margin('30%')
    }
  }
}
```

Button默认normal态显示绿色，第一次按下Tab键让Button获焦显示为focus态的红色，点击事件触发后，在此按下Tab键让Button获焦，focus态变为粉色。



## @AnimatableExtend装饰器：定义可动画属性

### 装饰器使用说明

#### 语法

```ts
@AnimatableExtend(UIComponentName) function functionName(value: typeName) { 
  .propertyName(value)
}
```



#### 使用场景

折线的动画效果



状态



## 状态管理概述

在前文的描述中，我们构建的页面多为静态界面。如果希望构建一个动态的、有交互的界面，就需要引入“状态”的概念。

在声明式UI编程框架中，UI是程序状态的运行结果，用户构建了一个UI模型，其中应用的运行时的状态是参数。当参数改变时，UI作为返回结果，也将进行对应的改变。这些运行时的状态变化所带来的UI的重新渲染，在ArkUI中统称为状态管理机制。

自定义组件拥有变量，变量必须被装饰器装饰才可以成为状态变量，状态变量的改变会引起UI的渲染刷新。如果不使用状态变量，UI只能在初始化时渲染，后续将不会再刷新。 下图展示了State和View（UI）之间的关系。

<img src="./ArkTS.assets/image-20240301113539540.png" alt="image-20240301113539540" style="zoom:50%;" />

+ View（UI）：UI渲染，指将build方法内的UI描述和@Builder装饰的方法内的UI描述映射到界面。
+ State：状态，指驱动UI更新的数据。用户通过触发组件的事件方法，改变状态数据。状态数据的改变，引起UI的重新渲染。



### 基本概念

+ **状态变量：被状态装饰器装饰的变量，状态变量值的改变会引起UI的渲染更新。示例：`@State num: number = 1`，其中，@State是状态装饰器，num是状态变量。**
+ **常规变量：没有被状态装饰器修饰的变量，通常应用于辅助计算。它的改变永远不会引起UI的刷新。以下示例中increaseBy变量为常规变量**。
+ 数据源/同步源：状态变量的原始来源，可以同步给不同的状态数据。通常意义为父组件传给子组件的数据

- 命名参数机制：父组件通过指定参数传递给子组件的状态变量，为父子传递同步参数的主要手段。示例：CompA: ({ aProp: this.aProp })。

- 从父组件初始化：父组件使用命名参数机制，将指定参数传递给子组件。子组件初始化的默认值在有父组件传值的情况下，会被覆盖。示例：

  ```ts
  @Component
  struct MyComponent {
    @State count: number = 0;
    private increaseBy: number = 1;
  
    build() {
    }
  }
  
  @Component
  struct Parent {
    build() {
      Column() {
        // 从父组件初始化，覆盖本地定义的默认值
        MyComponent({ count: 1, increaseBy: 2 })
      }
    }
  }
  ```

- 初始化子节点：父组件中状态变量可以传递给子组件，初始化子组件对应的状态变量。示例同上。

- 本地初始化：在变量声明的时候赋值，作为变量的默认值。示例：@State count: number = 0。



### 装饰器总览

ArkUI提供了多种装饰器，通过使用这些装饰器，状态变量不仅可以观察到在组件内的改变，还可以在不同组件层级间传递，比如父子组件、跨组件层级，也可以观察全局范围内的变化。根据状态变量的影响范围，将所有的装饰器可以大致分为：

+ 管理组件拥有状态的装饰器：组件级别的状态管理，可以观察组件内变化，和不同组件层级的变化，但需要唯一观察同一个组建树上，即同一个页面内。
+ 管理应用拥有状态的装饰器：应用级别的状态管理，可以观察不同页面，甚至不同UIAbility的状态变化，是应用内全局的状态管理。

从数据的传递形式和同步类型层面看，装饰器也可分为：

+ 只读的单向传递：
+ 可变更的双向传递。

图示如下，具体装饰器的介绍，可详见[管理组件拥有的状态](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md)和[管理应用拥有的状态](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-application-state-management-overview.md)。开发者可以灵活地利用这些能力来实现数据和UI的联动。

![image-20240301134648364](./ArkTS.assets/image-20240301134648364.png)

上图中，Components部分的装饰器为组件级别的状态管理，Application部分为应用的状态管理。开发者可以通过[@StorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storagelink)/[@LocalStorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstoragelink)实现应用和组件状态的双向同步，通过[@StorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storageprop)/[@LocalStorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstorageprop)实现应用和组件状态的单向同步。

[管理组件拥有的状态](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md)，即图中Components级别的状态管理：

- [@State](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md)：@State装饰的变量拥有其所属组件的状态，可以作为其子组件单向和双向同步的数据源。当其数值改变时，会引起相关组件的渲染刷新。
- [@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)：`@Prop`装饰的变量可以**和父组件建立单向同步关系**，@Prop装饰的变量是可变的，但修改不会同步回父组件。
- [@Link](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md)：`@Link`装饰的变量**和父组件构建双向同步关系的状态变量**，父组件会接受来自@Link装饰的变量的修改的同步，父组件的更新也会同步给@Link装饰的变量。
- [@Provide/@Consume](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-provide-and-consume.md)：@Provide/@Consume装饰的变量用于跨组件层级（多层组件）同步状态变量，可以不需要通过参数命名机制传递，通过alias（别名）或者属性名绑定。
- [@Observed](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md)：@Observed装饰class，需要观察多层嵌套场景的class需要被@Observed装饰。单独使用@Observed没有任何作用，需要和@ObjectLink、@Prop连用。
- [@ObjectLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md)：@ObjectLink装饰的变量接收@Observed装饰的class的实例，应用于观察多层嵌套场景，和父组件的数据源构建双向同步。

> **说明：**
>
> 仅[@Observed/@ObjectLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md)可以观察嵌套场景，其他的状态变量仅能观察第一层，详情见各个装饰器章节的“观察变化和行为表现”小节。

[管理应用拥有的状态](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-application-state-management-overview.md)，即图中Application级别的状态管理：

- [AppStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md)是应用程序中的一个特殊的单例[LocalStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md)对象，是应用级的数据库，和进程绑定，通过[@StorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storageprop)和[@StorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storagelink)装饰器可以和组件联动。
- AppStorage是应用状态的“中枢”，将需要与组件（UI）交互的数据存入AppStorage，比如持久化数据[PersistentStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md)和环境变量[Environment](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-environment.md)。UI再通过AppStorage提供的装饰器或者API接口，访问这些数据。
- 框架还提供了LocalStorage，AppStorage是LocalStorage特殊的单例。LocalStorage是应用程序声明的应用状态的内存“数据库”，通常用于页面级的状态共享，通过[@LocalStorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstorageprop)和[@LocalStorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstoragelink)装饰器可以和UI联动。

### 其他状态管理功能

[@Watch](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-watch.md)用于监听状态变量的变化。

[$$运算符](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-two-way-sync.md)：给内置组件提供TS变量的引用，使得TS变量和内置组件的内部状态保持同步。



## @State装饰器：组件内状态

@State装饰的变量，或称为状态变量，一旦变量拥有了状态属性，就和自定义组件的渲染绑定起来。当状态改变时，UI会发生对应的渲染改变。

在状态变量相关装饰器中，@State是最基础的，使变量拥有状态属性的装饰器，它也是大部分状态变量的数据源。

### 概述

@State装饰的变量，与声明式范式中的其他被装饰变量一样，是**私有的**，只能从组件内部访问，**在声明时必须指定其类型和本地初始化**。**初始化也可选择使用命名参数机制从父组件完成初始化**。

@State装饰的变量拥有一下特点：

+ @State装饰的变量与子组件中的@Prop装饰变量之间建立单向数据同步，与@Link、@ObjectLink装饰变量之间建立双向数据同步。
+ @State装饰的变量生命周期与其所属自定义组件的生命周期相同。



### 装饰器使用规则说明

| @State变量装饰器   | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 装饰器参数         | 无                                                           |
| 同步类型           | **不与父组件中任何类型的变量同步。**                         |
| 允许装饰的变量类型 | Object、class、string、number、boolean、enum类型，以及这些类型的数组。<br /> 支持Date类型。<br /> 支持类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md#观察变化)。<br /> 类型必须被指定。 <br />不支持any，不支持简单类型和复杂类型的联合类型，不允许使用undefined和null。<br />**说明：**不支持Length、ResourceStr、ResourceColor类型，Length、ResourceStr、ResourceColor为简单类型和复杂类型的联合类型。 |
| 被装饰变量的初始值 | 必须本地初始化。                                             |



### 变量的传递/访问规则说明

| 传递/访问          | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 从父组件初始化     | 可选，从父组件初始化或者本地初始化。如果从父组件初始化将会覆盖本地初始化。 支持父组件中常规变量（常规变量对@State赋值，只是数值的初始化，常规变量的变化不会触发UI刷新，只有状态变量才能触发UI刷新）、@State、@Link、@Prop、@Provide、@Consume、@ObjectLink、@StorageLink、@StorageProp、@LocalStorageLink和@LocalStorageProp装饰的变量，初始化子组件的@State。 |
| 用于初始化子组件   | @State装饰的变量支持初始化子组件的常规变量、@State、@Link、@Prop、@Provide。 |
| 是否支持组件外访问 | 不支持，只能在组件内访问。                                   |

图1 规则化规则图示

![image-20240301143550724](./ArkTS.assets/image-20240301143550724.png)



### 观察变化和行为表现：

并不是状态变量的所有更改都会引起UI刷新，只有可以被框架观察到的修改才会引起UI刷新。本小节将介绍什么样的修改才能被观察到，以及观察到变化后，框架的是怎么引起UI刷新的，即框架的行为表现是什么。

#### 1、观察变化

- 当装饰的数据类型为`boolean`、`string`、`number`类型时，可以**观察到数值的变化**。

  ```ts
  // for simple type
  @State count: number = 0;
  // value changing can be observed
  this.count = 1;
  ```

- 当装饰的数据类型为`class`或者`Object`时，可以**观察到自身的赋值的变化，和其属性赋值的变化**，即Object.keys(observedObject)返回的所有属性。例子如下。 声明ClassA和Model类。

  ```ts
    class ClassA {
      public value: string;
    
      constructor(value: string) {
        this.value = value;
      }
    }
    
    class Model {
      public value: string;
      public name: ClassA;
      constructor(value: string, a: ClassA) {
        this.value = value;
        this.name = a;
      }
    }
  ```

  @State装饰的类型是Model

  ```ts
  // class类型
  @State title: Model = new Model('Hello', new ClassA('World'));
  ```

  对@State装饰变量的赋值。

  ```ts
  // class类型赋值
  this.title = new Model('Hi', new ClassA('ArkUI'));
  ```

  对@State装饰变量的属性赋值。

  ```ts
  // class属性的赋值
  this.title.value = 'Hi';
  ```

  嵌套属性的赋值观察不到。

  ```ts
  // 嵌套的属性赋值观察不到
  this.title.name.value = 'ArkUI';
  ```

  >观察不到即不触发UI更新？应该是需要重新渲染。

- 当装饰的对象是`array`时，可以**观察到数组本身的赋值和添加、删除、更新数组的变化**。例子如下。 声明Model类。

  ```ts
  class Model {
    public value: number;
    constructor(value: number) {
      this.value = value;
    }
  }
  ```

  @State装饰的对象为Model类型数组时。

  ```ts
  // 数组类型
  @State title: Model[] = [new Model(11), new Model(1)];
  ```

  数组自身的赋值可以观察到。

  ```ts
  // 数组赋值
  this.title = [new Model(2)];
  ```

  数组项的赋值可以观察到。

  ```ts
  // 数组项赋值
  this.title[0] = new Model(2);
  ```

  删除数组项可以观察到。

  ```ts
  // 数组项更改
  this.title.pop();
  ```

  新增数组项可以观察到。

  ```ts
  // 数组项更改
  this.title.push(new Model(12));
  ```

  数组项中**属性的赋值观察不到**。

  ```ts
  // 嵌套的属性赋值观察不到
  this.title[0].value = 6;
  ```

- 当装饰的对象是`Date`时，可以**观察到Date整体的赋值**，同时可通过调用Date的接口`setFullYear`, `setMonth`, `setDate`, `setHours`, `setMinutes`, `setSeconds`, `setMilliseconds`, `setTime`, `setUTCFullYear`, `setUTCMonth`, `setUTCDate`, `setUTCHours`, `setUTCMinutes`, `setUTCSeconds`, `setUTCMilliseconds` 更新Date的属性。

  ```ts
  @Entry
  @Component
  struct DatePickerExample {
    @State selectedDate: Date = new Date('2021-08-08')
  
    build() {
      Column() {
        Button('set selectedDate to 2023-07-08')
          .margin(10)
          .onClick(() => {
            this.selectedDate = new Date('2023-07-08')
          })
        Button('increase the year by 1')
          .margin(10)
          .onClick(() => {
            this.selectedDate.setFullYear(this.selectedDate.getFullYear() + 1)
          })
        Button('increase the month by 1')
          .margin(10)
          .onClick(() => {
            this.selectedDate.setMonth(this.selectedDate.getMonth() + 1)
          })
        Button('increase the day by 1')
          .margin(10)
          .onClick(() => {
            this.selectedDate.setDate(this.selectedDate.getDate() + 1)
          })
        DatePicker({
          start: new Date('1970-1-1'),
          end: new Date('2100-1-1'),
          selected: this.selectedDate
        })
      }.width('100%')
    }
  }
  ```

#### 2、框架行为

- 当状态变量被改变时，查询依赖该状态变量的组件；
- 执行依赖该状态变量的组件的更新方法，组件更新渲染；
- 和该状态变量不相关的组件或者UI描述不会发生重新渲染，从而实现页面渲染的按需更新。

### 使用场景：

#### 1、装饰简单类型的变量

以下示例为@State装饰的简单类型，count被@State装饰成为状态变量，count的改变引起Button组件的刷新：

- 当状态变量count改变时，查询到只有Button组件关联了它；
- 执行Button组件的更新方法，实现按需刷新。

```ts
@Entry
@Component
struct MyComponent {
  @State count: number = 0;

  build() {
    Button(`click times: ${this.count}`)
      .onClick(() => {
        this.count += 1;
      })
  }
}
```

#### 2、装饰class对象类型的变量

- 自定义组件MyComponent定义了被@State装饰的状态变量count和title，其中title的类型为自定义类Model。如果count或title的值发生变化，则查询MyComponent中使用该状态变量的UI组件，并进行重新渲染。
- EntryComponent中有多个MyComponent组件实例，第一个MyComponent内部状态的更改不会影响第二个MyComponent。

```ts
class Model {
  public value: string;

  constructor(value: string) {
    this.value = value;
  }
}

@Entry
@Component
struct EntryComponent {
  build() {
    Column() {
      // 此处指定的参数都将在初始渲染时覆盖本地定义的默认值，并不是所有的参数都需要从父组件初始化
      MyComponent({ count: 1, increaseBy: 2 })
        .width(300)
      MyComponent({ title: new Model('Hello World 2'), count: 7 })
    }
  }
}

@Component
struct MyComponent {
  @State title: Model = new Model('Hello World');
  @State count: number = 0;
  private increaseBy: number = 1;

  build() {
    Column() {
      Text(`${this.title.value}`)
        .margin(10)
      Button(`Click to change title`)
        .onClick(() => {
          // @State变量的更新将触发上面的Text组件内容更新
          this.title.value = this.title.value === 'Hello ArkUI' ? 'Hello World' : 'Hello ArkUI';
        })
        .width(300)
        .margin(10)

      Button(`Click to increase count = ${this.count}`)
        .onClick(() => {
          // @State变量的更新将触发该Button组件的内容更新
          this.count += this.increaseBy;
        })
        .width(300)
        .margin(10)
    }
  }
}
```

从该示例中，我们可以了解到@State变量首次渲染的初始化流程：

1. 使用默认的本地初始化：

   ```ts
   @State title: Model = new Model('Hello World');
   @State count: number = 0;
   ```

2. 对于@State来说，命名参数机制传递的值并不是必选的，如果没有命名参数传值，则使用本地初始化的默认值：

   ```ts
   class C1 {
      public count:number;
      public increaseBy:number;
      constructor(count: number, increaseBy:number) {
        this.count = count;
        this.increaseBy = increaseBy;
     }
   }
   let obj = new C1(1, 2)
   MyComponent(obj)
   ```

### 常见问题：

#### 1、使用箭头函数改变状态变量未生效

**箭头函数体内的this对象，就是定义该函数时所在的作用域指向的对象**，而不是使用时所在的作用域指向的对象。**所以在该场景下， changeCoverUrl的this指向PlayDetailViewModel，而不是被装饰器@State代理的状态变量**。

反例：

```ts
export default class PlayDetailViewModel {
  coverUrl: string = '#00ff00'

  changeCoverUrl= ()=> {
    this.coverUrl = '#00F5FF'
  }

}

import PlayDetailViewModel from './PlayDetailViewModel'

@Entry
@Component
struct PlayDetailPage {
  @State vm: PlayDetailViewModel = new PlayDetailViewModel()

  build() {
    Stack() {
      Text(this.vm.coverUrl).width(100).height(100).backgroundColor(this.vm.coverUrl)
      Row() {
        Button('点击改变颜色')
          .onClick(() => {
            this.vm.changeCoverUrl()
          })
      }
    }
    .width('100%')
    .height('100%')
    .alignContent(Alignment.Top)
  }
}
```

所以要将当前this.vm传入，调用代理状态变量的属性赋值。

正例：

```ts
export default class PlayDetailViewModel {
  coverUrl: string = '#00ff00'

  changeCoverUrl= (model:PlayDetailViewModel)=> {
    model.coverUrl = '#00F5FF'
  }

}
ts
import PlayDetailViewModel from './PlayDetailViewModel'

@Entry
@Component
struct PlayDetailPage {
  @State vm: PlayDetailViewModel = new PlayDetailViewModel()

  build() {
    Stack() {
      Text(this.vm.coverUrl).width(100).height(100).backgroundColor(this.vm.coverUrl)
      Row() {
        Button('点击改变颜色')
          .onClick(() => {
            let self = this.vm
            this.vm.changeCoverUrl(self)
          })
      }
    }
    .width('100%')
    .height('100%')
    .alignContent(Alignment.Top)
  }
}
```

#### 2、状态变量的修改放在构造函数内未生效

**在状态管理中，类会被一层“代理”进行包装**。**当在组件中改变该类的成员变量时，会被该代理进行拦截，在更改数据源中值的同时，也会将变化通知给绑定的组件，从而实现观测变化与触发刷新**。当开发者把状态变量的修改放在构造函数里时，此修改不会经过代理（因为是直接对数据源中的值进行修改），即使修改成功执行，也无法观测UI的刷新。

【反例】

```ts
@Entry
@Component
struct Index {
  @State viewModel: TestModel = new TestModel();

  build() {
    Row() {
      Column() {
        Text(this.viewModel.isSuccess ? 'success' : 'failed')
          .fontSize(50)
          .fontWeight(FontWeight.Bold)
          .onClick(() => {
            this.viewModel.query()
          })
      }.width('100%')
    }.height('100%')
  }
}

export class TestModel {
  isSuccess: boolean = false
  model: Model

  constructor() {
    this.model = new Model(() => {
      this.isSuccess = true
      console.log(`this.isSuccess: ${this.isSuccess}`)
    })
  }

  query() {
    this.model.query()
  }
}

export class Model {
  callback: () => void

  constructor(cb: () => void) {
    this.callback = cb
  }

  query() {
    this.callback()
  }
}
```

上文示例代码将状态变量的修改放在构造函数内，界面开始时显示“failed”，点击后日志打印“this.isSuccess: true”说明修改成功，但界面依旧显示“failed”，未实现刷新。

【正例】

```ts
@Entry
@Component
struct Index {
  @State viewModel: TestModel = new TestModel();

  build() {
    Row() {
      Column() {
        Text(this.viewModel.isSuccess ? 'success' : 'failed')
          .fontSize(50)
          .fontWeight(FontWeight.Bold)
          .onClick(() => {
            this.viewModel.query()
          })
      }.width('100%')
    }.height('100%')
  }
}

export class TestModel {
  isSuccess: boolean = false
  model: Model = new Model(() => {
  })

  query() {
    this.model = new Model(() => {
      this.isSuccess = true
    })
    this.model.query()
  }
}

export class Model {
  callback: () => void

  constructor(cb: () => void) {
    this.callback = cb
  }

  query() {
    this.callback()
  }
}
ts
```

上文示例代码将状态变量的修改放在类的普通方法中，界面开始时显示“failed”，点击后显示“success”。



## @Prop装饰器：父子单向同步

@Prop装饰的变量可以和父组件建立单向的同步关系。@Prop装饰的变量是可变的，但是变化不会同步回其父组件。

### 概述

@Prop装饰的变量和父组件建立单向的同步关系：

+ @Prop变量允许在本地修改，但修改后的变化不会同步回父组件。
+ 当数据源更改时，@Prop装饰的变量都会更新，并且会覆盖本地所有更改。因此，数值的同步是父组件到子组件（所属组件），子组件数值的变化不会同步到父组件。

### 限制条件

+ @Prop装饰变量时会进行深拷贝，在拷贝的过程中除了基本类型、Map、Set、Date、Array外，都会丢失类型。例如PixelMap等通过NAPI提供的复杂类型，由于有部分实现在Native侧，因此无法在ArkTS侧通过深拷贝获得完整的数据。
+ @Prop装饰器**不能在@Entry装饰**的自定义组件中使用。

### 装饰器使用规则说明

| @Prop变量装饰器    | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 装饰器参数         | 无                                                           |
| 同步类型           | **单向同步**：对父组件状态变量值的修改，将同步给子组件@Prop装饰的变量，子组件@Prop变量的修改不会同步到父组件的状态变量上。嵌套类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#观察变化)。 |
| 允许装饰的变量类型 | Object、class、string、number、boolean、enum类型，以及这些类型的数组。 <br />不支持any，不支持简单类型和复杂类型的联合类型，不允许使用undefined和null。 <br />支持Date类型。 支持类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#观察变化)。 必须指定类型。 <br />**说明** ： 不支持Length、ResourceStr、ResourceColor类型，Length，ResourceStr、ResourceColor为简单类型和复杂类型的联合类型。 在父组件中，传递给@Prop装饰的值不能为undefined或者null，反例如下所示。 CompA ({ aProp: undefined }) CompA ({ aProp: null }) @Prop和[数据源](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state-management-overview.md#基本概念)类型需要相同，有以下三种情况：<br /> - @Prop装饰的变量和@State以及其他装饰器同步时双方的类型必须相同，示例请参考[父组件@State到子组件@Prop简单数据类型同步](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#父组件state到子组件prop简单数据类型同步)。<br /> - @Prop装饰的变量和@State以及其他装饰器装饰的数组的项同步时 ，@Prop的类型需要和@State装饰的数组的数组项相同，比如@Prop : T和@State : Array<T>，示例请参考[父组件@State数组中的项到子组件@Prop简单数据类型同步](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#父组件state数组项到子组件prop简单数据类型同步)； <br />- 当父组件状态变量为Object或者class时，@Prop装饰的变量和父组件状态变量的属性类型相同，示例请参考[从父组件中的@State类对象属性到@Prop简单类型的同步](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#从父组件中的state类对象属性到prop简单类型的同步)。 |
| 嵌套传递层数       | 在组件复用场景，建议@Prop深度嵌套数据不要超过5层，嵌套太多会导致深拷贝占用的空间过大以及GarbageCollection(垃圾回收)，引起性能问题，此时更建议使用[@ObjectLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md)。 |
| 被装饰变量的初始值 | 允许本地初始化。                                             |

### 变量的传递/访问规则说明

| 传递/访问          | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 从父组件初始化     | 如果本地有初始化，则是可选的。没有的话，则必选，支持父组件中的常规变量（常规变量对@Prop赋值，只是数值的初始化，常规变量的变化不会触发UI刷新。只有状态变量才能触发UI刷新）、@State、@Link、@Prop、@Provide、@Consume、@ObjectLink、@StorageLink、@StorageProp、@LocalStorageLink和@LocalStorageProp去初始化子组件中的@Prop变量。 |
| 用于初始化子组件   | @Prop支持去初始化子组件中的常规变量、@State、@Link、@Prop、@Provide。 |
| 是否支持组件外访问 | @Prop装饰的变量是私有的，只能在组件内访问。                  |

初始化规则图示

![image-20240304160149201](./ArkTS.assets/image-20240304160149201.png)



### 观察变化和行为表现：

#### 1、观察变化

@Prop装饰的数据可以观察到以下变化。

+ 当装饰的类型是允许的类型，即`Object、class、string、number、boolean、enum`类型都可以观察到赋值的变化。

  ```ts
  // 简单类型
  @Prop count: number;
  // 赋值的变化可以被观察到
  this.count = 1;
  // 复杂类型
  @Prop title: Model;
  // 可以观察到赋值的变化
  this.title = new Model('Hi');
  ```

+ 当装饰的类型是`Object`或者`class`复杂类型时，可以观察到**第一层的属性的变化**，属性即Object.keys(observedObject)返回的所有属性；

对于嵌套场景，如果class是被@Observed装饰的，可以观察到class属性的变化，示例请参考[@Prop嵌套场景](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#prop嵌套场景)。

+ 当装饰的类型是**数组**的时候，可以观察到**数组本身的赋值、添加、删除和更新**。

对于@State和@Prop的同步场景：

- 使用父组件中@State变量的值初始化子组件中的@Prop变量。当@State变量变化时，该变量值也会同步更新至@Prop变量（**覆盖本地更改**）。
- @Prop装饰的变量的修改不会影响其数据源@State装饰变量的值。
- 除了@State，数据源也可以用@Link或@Prop装饰，对@Prop的同步机制是相同的。
- 数据源和@Prop变量的类型需要相同，@Prop允许简单类型和class类型。
- 当装饰的对象是Date时，可以观察到Date整体的赋值，同时可通过调用Date的接口`setFullYear`, `setMonth`, `setDate`, `setHours`, `setMinutes`, `setSeconds`, `setMilliseconds`, `setTime`, `setUTCFullYear`, `setUTCMonth`, `setUTCDate`, `setUTCHours`, `setUTCMinutes`, `setUTCSeconds`, `setUTCMilliseconds` 更新Date的属性

#### 2、框架行为

要理解@Prop变量值初始化和更新机制，有必要了解父组件和拥有@Prop变量的子组件初始渲染和更新流程。

1. 初始渲染：
   1. 执行父组件的build()函数将创建子组件的新实例，将数据源传递给子组件；
   2. 初始化子组件@Prop装饰的变量。
2. 更新：
   1. 子组件@Prop更新时，更新仅停留在当前子组件，不会同步回父组件；
   2. 当父组件的数据源更新时，子组件的@Prop装饰的变量将被来自父组件的数据源重置，所有@Prop装饰的本地的修改将被父组件的更新覆盖。

> **说明：**
>
> **`@Prop`装饰的数据更新依赖其所属自定义组件的重新渲染，所以在应用进入后台后，@Prop无法刷新，推荐使用`@Link`代替。**



### 使用场景：

#### 1、父组件@State到子组件@Prop的简单数据类型同步

#### 2、父组件@State数组项到子组件@Prop简单数据类型同步

#### 3、从父组件中的@State类对象属性到@Prop简单类型的同步

如果图书馆有一本图书和两位用户，每位用户都可以将图书标记为已读，此标记行为不会影响其它读者用户。从代码角度讲，对@Prop图书对象的本地更改不会同步给图书馆组件中的@State图书对象。

在此示例中，图书类可以使用@Observed装饰器，但不是必须的，只有在嵌套结构时需要此装饰器。这一点我们会在[从父组件中的@State数组项到@Prop class类型的同步](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md#从父组件中的state数组项到prop-class类型的同步)说明。

#### 4、从父组件中的@State数组项到@Prop class类型的同步

在下面的示例中，更改了@State 装饰的allBooks数组中Book对象上的属性，但点击“Mark read for everyone”无反应。这是因为该属性是第二层的嵌套属性，@State装饰器只能观察到第一层属性，不会观察到此属性更改，所以框架不会更新ReaderComp。

```ts
let nextId: number = 1;

// @Observed
class Book {
  public id: number;
  public title: string;
  public pages: number;
  public readIt: boolean = false;

  constructor(title: string, pages: number) {
    this.id = nextId++;
    this.title = title;
    this.pages = pages;
  }
}

@Component
struct ReaderComp {
  @Prop book: Book = new Book("", 1);

  build() {
    Row() {
      Text(` ${this.book ? this.book.title : "Book is undefined"}`).fontColor('#e6000000')
      Text(` has ${this.book ? this.book.pages : "Book is undefined"} pages!`).fontColor('#e6000000')
      Text(` ${this.book ? this.book.readIt ? "I have read" : 'I have not read it' : "Book is undefined"}`).fontColor('#e6000000')
        .onClick(() => this.book.readIt = true)
    }
  }
}

@Entry
@Component
struct Library {
  @State allBooks: Book[] = [new Book("C#", 765), new Book("JS", 652), new Book("TS", 765)];

  build() {
    Column() {
      Text('library`s all time favorite')
        .width(312)
        .height(40)
        .backgroundColor('#0d000000')
        .borderRadius(20)
        .margin(12)
        .padding({ left: 20 })
        .fontColor('#e6000000')
      ReaderComp({ book: this.allBooks[2] })
        .backgroundColor('#0d000000')
        .width(312)
        .height(40)
        .padding({ left: 20, top: 10 })
        .borderRadius(20)
        .colorBlend('#e6000000')
      Divider()
      Text('Books on loaan to a reader')
        .width(312)
        .height(40)
        .backgroundColor('#0d000000')
        .borderRadius(20)
        .margin(12)
        .padding({ left: 20 })
        .fontColor('#e6000000')
      ForEach(this.allBooks, (book: Book) => {
        ReaderComp({ book: book })
          .margin(12)
          .width(312)
          .height(40)
          .padding({ left: 20, top: 10 })
          .backgroundColor('#0d000000')
          .borderRadius(20)
      },
        (book: Book) => book.id.toString())
      Button('Add new')
        .width(312)
        .height(40)
        .margin(12)
        .fontColor('#FFFFFF 90%')
        .onClick(() => {
          this.allBooks.push(new Book("JA", 512));
        })
      Button('Remove first book')
        .width(312)
        .height(40)
        .margin(12)
        .fontColor('#FFFFFF 90%')
        .onClick(() => {
          if (this.allBooks.length > 0){
            this.allBooks.shift();
          } else {
            console.log("length <= 0")
          }
        })
      Button("Mark read for everyone")
        .width(312)
        .height(40)
        .margin(12)
        .fontColor('#FFFFFF 90%')
        .onClick(() => {
          this.allBooks.forEach((book) => book.readIt = true)
        })
    }
  }
}
```

需要使用`@Observed`**装饰class Book**，**Book的属性将被观察**。 需要注意的是，@Prop在子组件装饰的状态变量和父组件的数据源是单向同步关系，即ReaderComp中的@Prop book的修改不会同步给父组件Library。而**父组件只会在数值有更新的时候（和上一次状态的对比），才会触发UI的重新渲染**。

```ts
@Observed
class Book {
  public id: number;
  public title: string;
  public pages: number;
  public readIt: boolean = false;

  constructor(title: string, pages: number) {
    this.id = nextId++;
    this.title = title;
    this.pages = pages;
  }
}
```

@Observed装饰的类的实例会被不透明的代理对象包装，此代理可以检测到包装对象内的所有属性更改。如果发生这种情况，此时，代理通知@Prop，@Prop对象值被更新。

#### 5、@Prop本地初始化不和父组件同步

**为了支持@Component装饰的组件复用场景，@Prop支持本地初始化，这样可以让@Prop是否与父组件建立同步关系变得可选**。当且仅当@Prop有本地初始化时，从父组件向子组件传递@Prop的数据源才是可选的。

#### 6、@Prop嵌套场景

在嵌套场景下，每一层都要用@Observed装饰，且每一层都要被@Prop接收，这样才能观察到嵌套场景。

### 常见问题

#### @Prop装饰状态变量未初始化错误

@Prop需要被初始化，如果没有进行本地初始化的，则必须通过父组件进行初始化。如果进行了本地初始化，那么是可以不通过父组件进行初始化的。



## @Link装饰器：父子双向同步

子组件中被@Link装饰的变量与其父组件中对应的数据源建立双向数据绑定。

### 概述

@Link装饰的变量与其父组件中的数据源共享相同的值。

### 限制条件

+ @Link装饰器**不能在@Entry装饰**的自定义组件中使用。

### 装饰器使用规则说明

| @Link变量装饰器    | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 装饰器参数         | 无                                                           |
| 同步类型           | **双向同步**。 父组件中`@State, @StorageLink和@Link `和子组件@Link可以建立双向数据同步，反之亦然。 |
| 允许装饰的变量类型 | Object、class、string、number、boolean、enum类型，以及这些类型的数组。 <br />支持Date类型。支持类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md#观察变化)。 <br />类型必须被指定，且和双向绑定状态变量的类型相同。<br /> 不支持any，不支持简单类型和复杂类型的联合类型，不允许使用undefined和null。<br /> **说明：** 不支持Length、ResourceStr、ResourceColor类型，Length、ResourceStr、ResourceColor为简单类型和复杂类型的联合类型。 |
| 被装饰变量的初始值 | 无，**禁止本地初始化。**                                     |



### 变量的传递/访问规则说明

| 传递/访问            | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父组件初始化和更新 | **必选**。与父组件@State, @StorageLink和@Link 建立双向绑定。允许父组件中`@State、@Link、@Prop、@Provide、@Consume、@ObjectLink、@StorageLink、@StorageProp、@LocalStorageLink和@LocalStorageProp`装饰变量初始化子组件@Link。 从API version 9开始，@Link子组件从父组件初始化@State的语法为Comp({ aLink: this.aState })。同样Comp({aLink: $aState})也支持。 |
| 用于初始化子组件     | 允许，可用于初始化常规变量、@State、@Link、@Prop、@Provide。 |
| 是否支持组件外访问   | 私有，只能在所属组件内访问。                                 |

初始化规则图示

![image-20240305085809318](./ArkTS.assets/image-20240305085809318.png)



### 观察变化和行为表现

#### 1、观察变化

+ 当装饰的数据类型为`boolean、string、number`类型时，可以同步观察到数值的变化，示例请参考[简单类型和类对象类型的@Link](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md#简单类型和类对象类型的link)。
+ 当装饰的数据类型为`class或者Object`时，可以观察到赋值和属性赋值的变化，即Object.keys(observedObject)返回的所有属性，示例请参考[简单类型和类对象类型的@Link](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md#简单类型和类对象类型的link)。
+ 当装饰的对象是`array`时，可以观察到数组添加、删除、更新数组单元的变化，示例请参考[数组类型的@Link](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-link.md#数组类型的link)。
+ 当装饰的对象是Date时，可以观察到Date整体的赋值，同时可通过调用Date的接口`setFullYear`, `setMonth`, `setDate`, `setHours`, `setMinutes`, `setSeconds`, `setMilliseconds`, `setTime`, `setUTCFullYear`, `setUTCMonth`, `setUTCDate`, `setUTCHours`, `setUTCMinutes`, `setUTCSeconds`, `setUTCMilliseconds` 更新Date的属性。



#### 2、框架行为

**@Link装饰的变量和其所属的自定义组件共享生命周期。**

为了了解@Link变量初始化和更新机制，有必要先了解父组件和拥有@Link变量的子组件的关系，初始渲染和双向更新的流程（以父组件为@State为例）。

1. 初始渲染：执行父组件的build()函数后将创建子组件的新实例。初始化过程如下：
   1. **必须指定父组件中的@State变量**，用于**初始化子组件的@Link变量**。子组件的@Link变量值与其父组件的数据源变量保持同步（**双向数据同步**）。
   2. 父组件的@State状态变量包装类通过构造函数传给子组件，子组件的@Link包装类拿到父组件的@State的状态变量后，将当前@Link包装类this指针注册给父组件的@State变量。
2. @Link的数据源的更新：即父组件中状态变量更新，引起相关子组件的@Link的更新。处理步骤：
   1. 通过初始渲染的步骤可知，子组件@Link包装类把当前this指针注册给父组件。父组件@State变量变更后，会遍历更新所有依赖它的系统组件（elementid）和状态变量（比如@Link包装类）。
   2. 通知@Link包装类更新后，子组件中所有依赖@Link状态变量的系统组件（elementId）都会被通知更新。以此实现父组件对子组件的状态数据同步。
3. @Link的更新：当子组件中@Link更新后，处理步骤如下（以父组件为@State为例）：
   1. @Link更新后，调用父组件的@State包装类的set方法，将更新后的数值同步回父组件。
   2. 子组件@Link和父组件@State分别遍历依赖的系统组件，进行对应的UI的更新。以此实现子组件@Link同步回父组件@State。



### 使用场景

#### 1、简单类型和类对象类型的@Link

#### 2、数组类型的@Link

```ts
@Component
struct Child {
  @Link items: number[];

  build() {
    Column() {
      Button(`Button1: push`)
        .margin(12)
        .width(312)
        .height(40)
        .fontColor('#FFFFFF，90%')
        .onClick(() => {
          this.items.push(this.items.length + 1);
        })
      Button(`Button2: replace whole item`)
        .margin(12)
        .width(312)
        .height(40)
        .fontColor('#FFFFFF，90%')
        .onClick(() => {
          this.items = [100, 200, 300];
        })
    }
  }
}

@Entry
@Component
struct Parent {
  @State arr: number[] = [1, 2, 3];

  build() {
    Column() {
      Child({ items: $arr })
        .margin(12)
      ForEach(this.arr,
        (item: number) => {
          Button(`${item}`)
            .margin(12)
            .width(312)
            .height(40)
            .backgroundColor('#11a2a2a2')
            .fontColor('#e6000000')
        },
        (item: ForEachInterface) => item.toString()
      )
    }
  }
}s
```

上文所述，ArkUI框架可以观察到数组元素的添加，删除和替换。在该示例中@State和@Link的类型是相同的number[]，**不允许将@Link定义成number类型（@Link item : number），并在父组件中用@State数组中每个数据项创建子组件。如果要使用这个场景，可以参考[@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)和@Observed。**

### 常见问题

#### @Link装饰状态变量类型错误

在子组件中使用@Link装饰状态变量需要保证**该变量与数据源类型完全相同**，且该数据源需为**被诸如@State等装饰器装饰的状态变量**。



## @Provide装饰器和@Consume装饰器：与后代组件双向同步

`@Provide和@Consume`，应用于与后代组件的双向数据同步，应用于状态数据在多个层级之间传递的场景。不同于上文提到的父子组件之间通过命名参数机制传递，@Provide和@Consume**摆脱参数传递机制的束缚**，**实现跨层级传递**。

其中@Provide装饰的变量是在祖先组件中，可以理解为被“提供”给后代的状态变量。@Consume装饰的变量是在后代组件中，去“消费（绑定）”祖先组件提供的变量。

### 概述

@Provide/@Consume装饰的状态变量有以下特性：

- @Provide装饰的状态变量自动对其所有后代组件可用，即该变量被“provide”给他的后代组件。由此可见，@Provide的方便之处在于，开发者**不需要多次在组件之间传递变量**。
- 后代通过使用@Consume去获取@Provide提供的变量，建立在@Provide和@Consume之间的双向数据同步，与@State/@Link不同的是，前者**可以在多层级的父子组件之间传递**。
- @Provide和@Consume可以**通过相同的变量名或者相同的变量别名绑定**，**建议类型相同**，否则会发生类型隐式转换，从而导致应用行为异常。

```ts
// 通过相同的变量名绑定
@Provide a: number = 0;
@Consume a: number;

// 通过相同的变量别名绑定
@Provide('a') b: number = 0;
@Consume('a') c: number;
```

@Provide和@Consume通过相同的变量名或者相同的变量别名绑定时，@Provide装饰的变量和@Consume装饰的变量是**一对多**的关系。**不允许在同一个自定义组件内**，包括其子组件中声明多个同名或者同别名的@Provide装饰的变量，@Provide的**属性名或别名需要唯一且确定**，如果声明多个同名或者同别名的@Provide装饰的变量，会发生运行时报错。

### 装饰器说明

@State的规则同样适用于@Provide，差异为@Provide还作为多层后代的同步源。

| @Provide变量装饰器 | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 装饰器参数         | 别名：常量字符串，可选。<br />**如果指定了别名，则通过别名来绑定变量；如果未指定别名，则通过变量名绑定变量**。 |
| 同步类型           | **双向同步**。<br /> 从@Provide变量到所有@Consume变量以及相反的方向的数据同步。双向同步的操作与@State和@Link的组合相同。 |
| 允许装饰的变量类型 | Object、class、string、number、boolean、enum类型，以及这些类型的数组。<br /> 支持Date类型。<br /> 支持类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-provide-and-consume.md#观察变化)。<br /> 不支持any，不支持简单类型和复杂类型的联合类型，不允许使用undefined和null。<br /> 必须指定类型。@Provide变量的@Consume变量的类型必须相同。<br /> **说明：** 不支持Length、ResourceStr、ResourceColor类型，Length、ResourceStr、ResourceColor为简单类型和复杂类型的联合类型。 |
| 被装饰变量的初始值 | **必须指定。**                                               |

| @Consume变量装饰器 | 说明                                                         |
| :----------------- | :----------------------------------------------------------- |
| 装饰器参数         | 别名：常量字符串，可选。 **如果提供了别名，则必须有@Provide的变量和其有相同的别名才可以匹配成功**；**否则，则需要变量名相同才能匹配成功**。 |
| 同步类型           | 双向：从@Provide变量（具体请参见@Provide）到所有@Consume变量，以及相反的方向。双向同步操作与@State和@Link的组合相同。 |
| 允许装饰的变量类型 | Object、class、string、number、boolean、enum类型，以及这些类型的数组。 <br />支持Date类型。 支持类型的场景请参考[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-provide-and-consume.md#观察变化)。 <br />不支持any，不允许使用undefined和null。<br /> 必须指定类型。@Provide变量和@Consume变量的类型必须相同。<br /> **说明：** @Consume装饰的变量，在其父节点或者祖先节点上，必须有对应的属性和别名的@Provide装饰的变量。 |
| 被装饰变量的初始值 | **无，禁止本地初始化。**                                     |

### 变量的传递/访问规则说明

| @Provide传递/访问    | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父组件初始化和更新 | 可选，**允许父组件中常规变量（常规变量对@Prop赋值，只是数值的初始化，常规变量的变化不会触发UI刷新，只有状态变量才能触发UI刷新）**、@State、@Link、@Prop、@Provide、@Consume、@ObjectLink、@StorageLink、@StorageProp、@LocalStorageLink和@LocalStorageProp装饰的变量装饰变量初始化子组件@Provide。 |
| 用于初始化子组件     | 允许，可用于初始化@State、@Link、@Prop、@Provide。           |
| 和父组件同步         | 否。                                                         |
| 和后代组件同步       | 和@Consume双向同步。                                         |
| 是否支持组件外访问   | 私有，仅可以在所属组件内访问。                               |

@Provide初始化规则图示



![image-20240305094427439](./ArkTS.assets/image-20240305094427439.png)

| @Consume传递/访问    | 说明                                                        |
| :------------------- | :---------------------------------------------------------- |
| 从父组件初始化和更新 | **禁止。通过相同的变量名和alias（别名）从@Provide初始化。** |
| 用于初始化子组件     | 允许，可用于初始化@State、@Link、@Prop、@Provide。          |
| 和祖先组件同步       | 和@Provide双向同步。                                        |
| 是否支持组件外访问   | 私有，仅可以在所属组件内访问                                |

@Consume初始化规则图示

![image-20240305101918636](./ArkTS.assets/image-20240305101918636.png)



### 观察行为和行为表现

#### 1、观察变化

+ 当装饰的数据类型为boolean、string、number类型时，可以观察到数值的变化。
+ 当装饰的数据类型为class或者Object的时候，可以观察到赋值和属性赋值的变化（属性为Object.keys(observedObject)返回的所有属性）。
+ 当装饰的对象是array的时候，可以观察到数组的添加、删除、更新数组单元。
+ 当装饰的对象是Date时，可以观察到Date整体的赋值，同时可通过调用Date的接口`setFullYear`, `setMonth`, `setDate`, `setHours`, `setMinutes`, `setSeconds`, `setMilliseconds`, `setTime`, `setUTCFullYear`, `setUTCMonth`, `setUTCDate`, `setUTCHours`, `setUTCMinutes`, `setUTCSeconds`, `setUTCMilliseconds` 更新Date的属性。

#### 2、框架行为

1. 初始渲染：

   1. @Provide装饰的**变量会以`map`的形式，传递给当前@Provide所属组件的所有子组件**；
   2. **子组件中如果使用`@Consume`变量，则会在map中查找是否有该变量名/alias（别名）对应的`@Provide`的变量**，**如果查找不到，框架会抛出JS ERROR**；
   3. 在初始化`@Consume`变量时，和@State/@Link的流程类似，**@Consume变量会保存在map中查找到的@Provide变量，并把自己注册给@Provide**。

2. 当@Provide装饰的数据变化时：

   1. 通过初始渲染的步骤可知，子组件@Consume已把自己注册给父组件**。父组件@Provide变量变更后，会遍历更新所有依赖它的系统组件（elementid）和状态变量（@Consume）**；
   2. 通知@Consume更新后，**子组件所有依赖@Consume的系统组件（elementId）都会被通知更新。以此实现@Provide对@Consume状态数据同步**。

3. 当@Consume装饰的数据变化时：

   通过初始渲染的步骤可知，子组件@Consume持有@Provide的实例。**在@Consume更新后调用@Provide的更新方法，将更新的数值同步回@Provide，以此实现@Consume向@Provide的同步更新**。



### 常见问题

#### @BuilderParam尾随闭包情况下@Provider未定义错误

在此场景下，CustomWidget执行this.builder()创建子组件CustomWidgetChild时，this指向的是HomePage。因此找不到CustomWidget的@Provide变量，所以下面示例会报找不到@Provide错误，和@BuilderParam连用的时候要谨慎this的指向。

错误示例：

```ts
class Tmp {
  a: string = ''
}

@Entry
@Component
struct HomePage {
  @Builder
  builder2($$: Tmp) {
    Text(`${$$.a}测试`)
  }
  build() {
    Column() {
      CustomWidget() {
        CustomWidgetChild({ builder: this.builder2 })
      }
    }
  }
}
@Component
struct CustomWidget {
  @Provide('a') a: string = 'abc';
  @BuilderParam
  builder: () => void;
  build() {
    Column() {
      Button('你好').onClick((x) => {
        if (this.a == 'ddd') {
          this.a = 'abc';
        }
        else {
          this.a = 'ddd';
        }
      })
      this.builder()
    }
  }
}
@Component
struct CustomWidgetChild {
  @Consume('a') a: string;
  @BuilderParam
  builder: ($$: Tmp) => void;
  build() {
    Column() {
      this.builder({ a: this.a })
    }
  }
}
```



## @Observed装饰器和@ObjectLink装饰器：嵌套类对象属性变化

**上文所述的装饰器仅能观察到第一层的变化**，但是在实际应用开发中，应用会根据开发需要，封装自己的数据模型。对于多层嵌套的情况，比如**二维数组，或者数组项class，或者class的属性是class**，他们的第二层的属性变化是无法观察到的。这就**引出了`@Observed/@ObjectLink`装饰器**。

### 概述

@ObjectLink和@Observed类装饰器用于在**涉及嵌套对象或数组**的场景中进行**双向**数据同步：

- **被@Observed装饰的类，可以被观察到属性的变化**；
- **子组件中`@ObjectLink`装饰器装饰的状态变量用于接收`@Observed`装饰的类的实例**，和父组件中对应的状态变量建立**双向**数据绑定。这个实例可以是数组中的被@Observed装饰的项，或者是class object中的属性，这个属性同样也需要被@Observed装饰。
- 单独使用@Observed是没有任何作用的，需要**搭配@ObjectLink或者[@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)使用**。

### 限制条件

- 使用@Observed装饰class会**改变class原始的原型链**，**@Observed和其他类装饰器装饰同一个class可能会带来问题**。
- **@ObjectLink装饰器不能在@Entry装饰的自定义组件中使用**。

### 装饰器说明

| @Observed类装饰器 | 说明                                                        |
| :---------------- | :---------------------------------------------------------- |
| 装饰器参数        | 无                                                          |
| 类装饰器          | **装饰`class`**。需要放在class的定义前，使用new创建类对象。 |

| @ObjectLink变量装饰器 | 说明                                                         |
| :-------------------- | :----------------------------------------------------------- |
| 装饰器参数            | 无                                                           |
| 同步类型              | **不与父组件中的任何类型同步变量**。                         |
| 允许装饰的变量类型    | **必须为被@Observed装饰的class实例，必须指定类型**。<br />**不支持简单类型，可以使用[@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)**。<br /> 支持继承Date或者Array的class实例，示例见[观察变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md#观察变化)。<br /> **@ObjectLink的属性是可以改变的，但是变量的分配是不允许的，也就是说这个装饰器装饰变量是只读的，不能被改变**。 |
| 被装饰变量的初始值    | **不允许**。                                                 |

@ObjectLink装饰的数据为可读示例。

```ts
// 允许@ObjectLink装饰的数据属性赋值
this.objLink.a= ...
// 不允许@ObjectLink装饰的数据自身赋值
this.objLink= ...
```

> **说明：**
>
> @ObjectLink装饰的变量不能被赋值，**如果要使用赋值操作，请使用[@Prop](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-prop.md)**。
>
> - @Prop装饰的变量和数据源的关系是是**单向同步**，@Prop装饰的变量在**本地拷贝了数据源**，**所以它允许本地更改**，如果父组件中的数据源有更新，@Prop装饰的变量本地的修改将被覆盖；
> - @ObjectLink装饰的变量和数据源的关系是**双向同步**，**@ObjectLink装饰的变量相当于指向数据源的指针**。禁止对@ObjectLink装饰的变量赋值，如果一旦发生@ObjectLink装饰的变量的赋值，则同步链将被打断。因为@ObjectLink装饰的变量通过数据源（Object）引用来初始化。对于实现双向数据同步的@ObjectLink，赋值相当于更新父组件中的数组项或者class的属性，TypeScript/JavaScript不能实现，会发生运行时报错。

### 变量的传递/访问规则说明

| @ObjectLink传递/访问 | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父组件初始化       | **必须指定**。 初始化@ObjectLink装饰的变量必须同时满足以下场景： <br />- 类型必须是@Observed装饰的class。<br /> - 初始化的数值需要是数组项，或者class的属性。<br /> - 同步源的class或者数组必须是@State，@Link，@Provide，@Consume或者@ObjectLink装饰的数据。 <br />同步源是数组项的示例请参考[对象数组](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md#对象数组)。初始化的class的示例请参考[嵌套对象](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md#嵌套对象)。 |
| 与源对象同步         | **双向**。                                                   |
| 可以初始化子组件     | 允许，可用于初始化常规变量、@State、@Link、@Prop、@Provide   |

初始化规则图示

![image-20240305113438948](./ArkTS.assets/image-20240305113438948.png)

### 观察变化和行为表现

#### 1、观察变化

@Observed装饰的类，如果**其属性为非简单类型**，比如**`class`、`Object`或者`数组`**，也需要被@Observed装饰，否则将观察不到其属性的变化。

```ts
class ClassA {
  public c: number;

  constructor(c: number) {
    this.c = c;
  }
}

@Observed
class ClassB {
  public a: ClassA;
  public b: number;

  constructor(a: ClassA, b: number) {
    this.a = a;
    this.b = b;
  }
}
```

以上示例中，ClassB被@Observed装饰，其成员变量的赋值的变化是可以被观察到的，但对于ClassA，没有被Observed装饰，其属性的修改不能被观察到。

```ts
@ObjectLink b: ClassB

// 赋值变化可以被观察到
this.b.a = new ClassA(5)
this.b.b = 5

// ClassA没有被@Observed装饰，其属性的变化观察不到
this.b.a.c = 5
```

@ObjectLink：@ObjectLink只能接收被@Observed装饰class的实例，可以观察到：

- 其属性的数值的变化，其中属性是指Object.keys(observedObject)返回的所有属性，示例请参考[嵌套对象](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md#嵌套对象)。
- 如果数据源是数组，则可以观察到数组item的替换，如果数据源是class，可观察到class的属性的变化，示例请参考[对象数组](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-observed-and-objectlink.md#对象数组)。

继承Date的class时，可以观察到Date整体的赋值，同时可通过调用Date的接口`setFullYear`, `setMonth`, `setDate`, `setHours`, `setMinutes`, `setSeconds`, `setMilliseconds`, `setTime`, `setUTCFullYear`, `setUTCMonth`, `setUTCDate`, `setUTCHours`, `setUTCMinutes`, `setUTCSeconds`, `setUTCMilliseconds` 更新Date的属性。

#### 2、框架行为

1. 初始渲染：
   1. **@Observed装饰的class的实例会被不透明的代理对象包装，代理了class上的属性的`setter`和`getter`方法**
   2. **子组件中@ObjectLink装饰的从父组件初始化，接收被@Observed装饰的class的实例，`@ObjectLink`的包装类会将自己注册给@Observed class**。
2. 属性更新：当@Observed装饰的class属性改变时，会**走到代理的setter和getter**，然后遍历依赖它的@ObjectLink包装类，通知数据更新。

### 使用场景

#### 嵌套对象

以下是嵌套类对象的数据结构

> **说明：**
>
> NextID是用来在[ForEach循环渲染](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md)过程中，为每个数组元素生成一个唯一且持久的键值，用于标识对应的组件。

```ts
// objectLinkNestedObjects.ets
let NextID: number = 1;

@Observed
class ClassA {
  public id: number;
  public c: number;

  constructor(c: number) {
    this.id = NextID++;
    this.c = c;
  }
}

@Observed
class ClassB {
  public a: ClassA;

  constructor(a: ClassA) {
    this.a = a;
  }
}

@Observed
class ClassD {
  public c: ClassC;

  constructor(c: ClassC) {
    this.c = c;
  }
}

@Observed
class ClassC extends ClassA {
  public k: number;

  constructor(k: number) {
    // 调用父类方法对k进行处理
    super(k);
    this.k = k;
  }
}
```

以下组件层次结构呈现的是此数据结构

```ts
@Component
struct ViewC {
  label: string = 'ViewC1';
  @ObjectLink c: ClassC;

  build() {
    Row() {
      Column() {
        Text(`ViewC [${this.label}] this.a.c = ${this.c.c}`)
          .fontColor('#ffffffff')
          .backgroundColor('#ff3fc4c4')
          .height(50)
          .borderRadius(25)
        Button(`ViewC: this.c.c add 1`)
          .backgroundColor('#ff7fcf58')
          .onClick(() => {
            this.c.c += 1;
            console.log('this.c.c:' + this.c.c)
          })
      }
      .width(300)
    }
  }
}

@Entry
@Component
struct ViewB {
  @State b: ClassB = new ClassB(new ClassA(0));
  @State child: ClassD = new ClassD(new ClassC(0));

  build() {
    Column() {
      ViewC({ label: 'ViewC #3',
        c: this.child.c })
      Button(`ViewC: this.child.c.c add 10`)
        .backgroundColor('#ff7fcf58')
        .onClick(() => {
          this.child.c.c += 10
          console.log('this.child.c.c:' + this.child.c.c)
        })
    }
  }
}
```

被@Observed装饰的ClassC类，可以观测到继承基类的属性的变化。

ViewB中的事件句柄：

- this.child.c = new ClassA(0) 和this.b = new ClassB(new ClassA(0))： 对@State装饰的变量b和其属性的修改。
- this.child.c.c = … ：该变化属于第二层的变化，@State无法观察到第二层的变化，但是ClassA被@Observed装饰，ClassA的属性c的变化可以被@ObjectLink观察到。

ViewC中的事件句柄：

- this.c.c += 1：对@ObjectLink变量a的修改，将触发Button组件的刷新。@ObjectLink和@Prop不同，@ObjectLink不拷贝来自父组件的数据源，而是在本地构建了指向其数据源的引用。
- @ObjectLink变量是只读的，this.a = new ClassA(…)是不允许的，因为一旦赋值操作发生，指向数据源的引用将被重置，同步将被打断。



#### 对象数组

对象数组是一种常用的数据结构。以下示例展示了数组对象的用法。

```ts
let NextID: number = 1;

@Observed
class ClassA {
  public id: number;
  public c: number;

  constructor(c: number) {
    this.id = NextID++;
    this.c = c;
  }
}

@Component
struct ViewA {
  // 子组件ViewA的@ObjectLink的类型是ClassA
  @ObjectLink a: ClassA;
  label: string = 'ViewA1';

  build() {
    Row() {
      Button(`ViewA [${this.label}] this.a.c = ${this.a ? this.a.c : "undefined"}`)
        .onClick(() => {
          this.a.c += 1;
        })
    }
  }
}

@Entry
@Component
struct ViewB {
  // ViewB中有@State装饰的ClassA[]
  @State arrA: ClassA[] = [new ClassA(0), new ClassA(0)];

  build() {
    Column() {
      ForEach(this.arrA,
        (item: ClassA) => {
          ViewA({ label: `#${item.id}`, a: item })
        },
        (item: ClassA): string => item.id.toString()
      )
      // 使用@State装饰的数组的数组项初始化@ObjectLink，其中数组项是被@Observed装饰的ClassA的实例
      ViewA({ label: `ViewA this.arrA[first]`, a: this.arrA[0] })
      ViewA({ label: `ViewA this.arrA[last]`, a: this.arrA[this.arrA.length-1] })

      Button(`ViewB: reset array`)
        .onClick(() => {
          this.arrA = [new ClassA(0), new ClassA(0)];
        })
      Button(`ViewB: push`)
        .onClick(() => {
          this.arrA.push(new ClassA(0))
        })
      Button(`ViewB: shift`)
        .onClick(() => {
          if (this.arrA.length > 0) {
            this.arrA.shift()
          } else {
            console.log("length <= 0")
          }
        })
      Button(`ViewB: chg item property in middle`)
        .onClick(() => {
          this.arrA[Math.floor(this.arrA.length / 2)].c = 10;
        })
      Button(`ViewB: chg item property in middle`)
        .onClick(() => {
          this.arrA[Math.floor(this.arrA.length / 2)] = new ClassA(11);
        })
    }
  }
}
```

- this.arrA[Math.floor(this.arrA.length/2)] = new ClassA(…) ：该状态变量的改变触发2次更新：
  1. ForEach：数组项的赋值导致ForEach的[itemGenerator](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md#接口描述)被修改，因此数组项被识别为有更改，ForEach的item builder将执行，创建新的ViewA组件实例。
  2. ViewA({ label: `ViewA this.arrA[last]`, a: this.arrA[this.arrA.length-1] })：上述更改改变了数组中第二个元素，所以绑定this.arrA[1]的ViewA将被更新；
- this.arrA.push(new ClassA(0)) ： 将触发2次不同效果的更新：
  1. **ForEach：新添加的ClassA对象对于ForEach是未知的[itemGenerator](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md#接口描述)，ForEach的item builder将执行，创建新的ViewA组件实例。**
  2. **ViewA({ label: `ViewA this.arrA[last]`, a: this.arrA[this.arrA.length-1] })：数组的最后一项有更改，因此引起第二个ViewA的实例的更改。对于ViewA({ label: `ViewA this.arrA[first]`, a: this.arrA[0] })，数组的更改并没有触发一个数组项更改的改变，所以第一个ViewA不会刷新。**
- this.arrA[Math.floor(this.arrA.length/2)].c：@State无法观察到第二层的变化，但是ClassA被@Observed装饰，ClassA的属性的变化将被@ObjectLink观察到。



#### 二维数组

使用@Observed观察二维数组的变化。可以声明一个被@Observed装饰的继承Array的子类。

```ts
@Observed
class StringArray extends Array<String> {
}
```

使用new StringArray()来构造StringArray的实例，new运算符使得@Observed生效，@Observed观察到StringArray的属性变化。

声明一个从Array扩展的类class StringArray extends Array\<String\> {}，并创建StringArray的实例。**@Observed装饰的类需要使用new运算符来构建class实例**。

```ts
@Observed
class StringArray extends Array<String> {
}

@Component
struct ItemPage {
  @ObjectLink itemArr: StringArray;

  build() {
    Row() {
      Text('ItemPage')
        .width(100).height(100)

      ForEach(this.itemArr,
        (item: string | Resource) => {
          Text(item)
            .width(100).height(100)
        },
        (item: string) => item
      )
    }
  }
}

@Entry
@Component
struct IndexPage {
  @State arr: Array<StringArray> = [new StringArray(), new StringArray(), new StringArray()];

  build() {
    Column() {
      ItemPage({ itemArr: this.arr[0] })
      ItemPage({ itemArr: this.arr[1] })
      ItemPage({ itemArr: this.arr[2] })
      Divider()


      ForEach(this.arr,
        (itemArr: StringArray) => {
          ItemPage({ itemArr: itemArr })
        },
        (itemArr: string) => itemArr[0]
      )

      Divider()

      Button('update')
        .onClick(() => {
          console.error('Update all items in arr');
          if ((this.arr[0] as Array<String>)[0] !== undefined) {
            // 正常情况下需要有一个真实的ID来与ForEach一起使用，但此处没有
            // 因此需要确保推送的字符串是唯一的。
            this.arr[0].push(`${this.arr[0].slice(-1).pop()}${this.arr[0].slice(-1).pop()}`);
            this.arr[1].push(`${this.arr[1].slice(-1).pop()}${this.arr[1].slice(-1).pop()}`);
            this.arr[2].push(`${this.arr[2].slice(-1).pop()}${this.arr[2].slice(-1).pop()}`);
          } else {
            this.arr[0].push('Hello');
            this.arr[1].push('World');
            this.arr[2].push('!');
          }
        })
    }
  }
}
```





### 常见问题

#### 在子组件中给@ObjectLink装饰的变量赋值

在子组件中给@ObjectLink装饰的变量赋值是不允许的。

> 对于实现双向数据同步的@ObjectLink，赋值相当于要更新父组件中的数组项或者class的属性，这个对于 TypeScript/JavaScript是不能实现的。框架对于这种行为会发生运行时报错。



#### 基础嵌套对象属性更改失效

在应用开发中，有很多嵌套对象场景，例如，开发者更新了某个属性，但UI没有进行对应的更新。

每个装饰器都有自己可以观察的能力，并不是所有的改变都可以被观察到，只有可以被观察到的变化才会进行UI更新。@Observed装饰器可以观察到嵌套对象的属性变化，其他装饰器仅能观察到第二层的变化。



#### 复杂嵌套对象属性更改失效

（我理解是通过嵌套@Component与@OjectLink搭配使用）实现“两个层级”的观察，即外部对象和内部嵌套对象的观察。但是该方法只能用于@ObjectLink装饰器，无法作用于@Prop（@Prop通过深拷贝传入对象）。详情参考@Prop与@ObjectLink的差异。



#### @Prop与@ObjectLink的差异

在下面的示例代码中，@ObjectLink装饰的变量是对数据源的引用，即在this.value.subValue和this.subValue都是同一个对象的不同引用，所以在点击CounterComp的click handler，改变this.value.subCounter.counter，this.subValue.counter也会改变，对应的组件Text(\`this.subValue.counter: ${this.subValue.counter}\`)会刷新。

```ts
let nextId = 1;

@Observed
class SubCounter {
  counter: number;

  constructor(c: number) {
    this.counter = c;
  }
}

@Observed
class ParentCounter {
  id: number;
  counter: number;
  subCounter: SubCounter;

  incrCounter() {
    this.counter++;
  }

  incrSubCounter(c: number) {
    this.subCounter.counter += c;
  }

  setSubCounter(c: number): void {
    this.subCounter.counter = c;
  }

  constructor(c: number) {
    this.id = nextId++;
    this.counter = c;
    this.subCounter = new SubCounter(c);
  }
}

@Component
struct CounterComp {
  @ObjectLink value: ParentCounter;

  build() {
    Column({ space: 10 }) {
      CountChild({ subValue: this.value.subCounter })
      Text(`this.value.counter：increase 7 `)
        .fontSize(30)
        .onClick(() => {
          // click handler, Text(`this.subValue.counter: ${this.subValue.counter}`) will update
          this.value.incrSubCounter(7);
        })
      Divider().height(2)
    }
  }
}

@Component
struct CountChild {
  @ObjectLink subValue: SubCounter;

  build() {
    Text(`this.subValue.counter: ${this.subValue.counter}`)
      .fontSize(30)
  }
}

@Entry
@Component
struct ParentComp {
  @State counter: ParentCounter[] = [new ParentCounter(1), new ParentCounter(2), new ParentCounter(3)];

  build() {
    Row() {
      Column() {
        CounterComp({ value: this.counter[0] })
        CounterComp({ value: this.counter[1] })
        CounterComp({ value: this.counter[2] })
        Divider().height(5)
        ForEach(this.counter,
          (item: ParentCounter) => {
            CounterComp({ value: item })
          },
          (item: ParentCounter) => item.id.toString()
        )
        Divider().height(5)
        Text('Parent: reset entire counter')
          .fontSize(20).height(50)
          .onClick(() => {
            this.counter = [new ParentCounter(1), new ParentCounter(2), new ParentCounter(3)];
          })
        Text('Parent: incr counter[0].counter')
          .fontSize(20).height(50)
          .onClick(() => {
            this.counter[0].incrCounter();
            this.counter[0].incrSubCounter(10);
          })
        Text('Parent: set.counter to 10')
          .fontSize(20).height(50)
          .onClick(() => {
            this.counter[0].setSubCounter(10);
          })
      }
    }
  }
}
```

@ObjectLink图示如下：

![image-20240305144328479](./ArkTS.assets/image-20240305144328479.png)

@Prop拷贝的关系图示如下：

![image-20240305144927340](./ArkTS.assets/image-20240305144927340.png)





#### 在@Observed装饰类的构造函数中延时更改成员变量

在状态管理中，使用@Observed装饰类后，会给该类使用一层“代理”进行包装。当在组件中改变该类的成员变量时，会被该代理进行拦截，在更改数据源中值的同时，也会将变化通知给绑定的组件，从而实现观测变化与触发刷新。当开发者在类的构造函数中对成员变量进行赋值或者修改时，此修改不会经过代理（因为是直接对数据源中的值进行修改），也就无法被观测到。所以，如果开发者在类的构造函数中使用定时器修改类中的成员变量，即使该修改成功执行了，也不会触发UI的刷新。



#### 在@Observed装饰的类内使用static方法进行初始化

在@Observed装饰的类内，尽量避免使用static方法进行初始化，在创建时会绕过Observed的实现，导致无法被代理，UI不刷新。





## 管理应用拥有的状态概述

上一章节中介绍的装饰器仅能在页面内，即一个组件树上共享状态变量。如果开发者要实现应用级的，或者多个页面的状态数据共享，就需要用到应用级别的状态管理的概念。ArkTS根据不同特性，提供了多种应用状态管理的能力：

+ [LocalStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md)：**页面级UI状态存储**，通常用于[UIAbility](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/apis/js-apis-app-ability-uiAbility.md)内、页面间的状态共享。
+ [AppStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md)：**特殊的单例LocalStorage对象**，由UI框架在应用程序启动时创建，**为应用程序UI状态属性提供中央存储**；
+ [PersistentStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md)：**持久化存储UI状态**，通常和AppStorage配合使用，选择AppStorage存储的数据写入磁盘，以确保这些属性在应用程序重新启动时的值与应用程序关闭时的值相同；
+ [Environment](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-environment.md)：应用程序运行的**设备的环境参数**，环境参数**会同步到AppStorage中**，可以和AppStorage搭配使用。



## LocalStorage：页面级UI状态存储

LocalStorage是页面级的UI状态存储，通过@Entry装饰器接收的参数可以在页面内共享同一个LocalStorage实例。LocalStorage支持UIAbility实例内多个页面间状态共享。

本文仅介绍LocalStorage使用场景和相关的装饰器：@LocalStorageProp和@LocalStorageLink。



### 概述

LocalStorage是ArkTS为构建页面级别状态变量提供存储的内存内“数据库”。

+ **应用程序可以创建多个LocalStorage实例**，LocalStorage实例可以在页面内共享，也可以通过GetShared接口，实现跨页面、UIAbility实例内共享。
+ **组件数的根节点**，即被@Entry装饰的@Component，可以被分配一个LocalStorage实例，此组件的所有子组件实例将自动获得对该LocalStorage实例的访问权限。
+ **被@Component装饰的组件最多可以访问一个LocalStorage实例和[AppStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md)**，**未被@Entry装饰的组件不可被独立分配LocalStorage实例，只能接收父组件通过@Entry传递来的LocalStorage实例**。**一个LocalStorage实例在组件树上可以被分配给多个组件**。
+ LocalStorage中的**所有属性都是可变的**。

应用程序决定LocalStorage对象的生命周期。当应用释放最后一个指向LocalStorage的引用时，比如销毁最后一个自定义组件，LocalStorage将被JS Engine垃圾回收。

LocalStorage根据与@Component装饰的组件的同步类型不同，提供了两个装饰器：

- [@LocalStorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstorageprop)：@LocalStorageProp装饰的变量和与LocalStorage中给定属性建立单向同步关系。
- [@LocalStorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#localstoragelink)：@LocalStorageLink装饰的变量和在@Component中创建与LocalStorage中给定属性建立双向同步关系。



### 限制条件

- LocalStorage创建后，命名属性的类型不可更改。后续调用Set时必须使用相同类型的值。
- LocalStorage是页面级存储，[getShared](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-state-management.md#getshared10)接口仅能获取当前Stage通过[windowStage.loadContent](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/apis/js-apis-window.md#loadcontent9)传入的LocalStorage实例，否则返回undefined。例子可见[将LocalStorage实例从UIAbility共享到一个或多个视图](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#将localstorage实例从uiability共享到一个或多个视图)。



### @LocalStorageProp

在上文中已经提到，如果要建立LocalStorage和自定义组件的联系，需要使用@LocalStorageProp和@LocalStorageLink装饰器。使用`@LocalStorageProp(key)/@LocalStorageLink(key)`装饰组件内的变量，**key标识了LocalStorage的属性**。

当自定义组件初始化的时候，@LocalStorageProp(key)/@LocalStorageLink(key)装饰的变量会**通过给定的key，绑定LocalStorage对应的属性，完成初始化**。本地初始化是必要的，因为无法保证LocalStorage一定存在给定的key（这取决于应用逻辑是否在组件初始化之前在LocalStorage实例中存入对应的属性）。

@LocalStorageProp(key)是和LocalStorage中key对应的属性建立单向数据同步，ArkUI框架支持修改@LocalStorageProp(key)在本地的值，但是对本地值的修改不会同步回LocalStorage中。相反，如果LocalStorage中key对应的属性值发生改变，例如通过set接口对LocalStorage中的值进行修改，改变会同步给@LocalStorageProp(key)，并覆盖掉本地的值。

#### 装饰器使用规则说明

| @LocalStorageProp变量装饰器 | 说明                                                         |
| :-------------------------- | :----------------------------------------------------------- |
| 装饰器参数                  | key：常量字符串，必填（字符串需要有引号）。                  |
| 允许装饰的变量类型          | Object、class、string、number、boolean、enum类型，以及这些类型的数组。嵌套类型的场景请参考[观察变化和行为表现](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#观察变化和行为表现)。 类型必须被指定，建议和LocalStorage中对应属性类型相同，否则会发生类型隐式转换，从而导致应用行为异常。不支持any，不允许使用undefined和null。 |
| 同步类型                    | **单向**同步：从LocalStorage的对应属性到组件的状态变量。组件本地的修改是允许的，但是LocalStorage中给定的属性一旦发生变化，将覆盖本地的修改。 |
| 被装饰变量的初始值          | **必须指定**，如果LocalStorage实例中不存在属性，则作为初始化默认值，并存入LocalStorage中。 |

#### 变量的传递/访问规则说明

| 传递/访问            | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父节点初始化和更新 | **禁止**，**@LocalStorageProp不支持从父节点初始化，只能从LocalStorage中key对应的属性初始化，如果没有对应key的话，将使用本地默认值初始化。** |
| 初始化子节点         | **支持**，可用于初始化@State、@Link、@Prop、@Provide。       |
| 是否支持组件外访问   | 否。                                                         |

@LocalStorageProp初始化规则

![image-20240305161225582](./ArkTS.assets/image-20240305161225582.png)



#### 观察变化和行为表现

##### 1、观察变化

- 当装饰的数据类型为`boolean、string、number`类型时，可以观察到数值的变化。
- 当装饰的数据类型为`class`或者`Object`时，可以观察到赋值和属性赋值的变化，即Object.keys(observedObject)返回的所有属性。
- 当装饰的对象是`array`时，可以观察到数组添加、删除、更新数组单元的变化。

##### 2、框架行为

- 当@LocalStorageProp(key)装饰的数值改变被观察到时，修改不会被同步回LocalStorage对应属性键值key的属性中。
- 当前@LocalStorageProp(key)单向绑定的数据会被修改，即仅限于当前组件的私有成员变量改变，其他的绑定该key的数据不会同步改变。
- 当@LocalStorageProp(key)装饰的数据本身是状态变量，它的改变虽然不会同步回LocalStorage中，但是**会引起所属的自定义组件的重新渲染**。
- 当LocalStorage中key对应的属性发生改变时，会同步给所有@LocalStorageProp(key)装饰的数据，@LocalStorageProp(key)本地的修改将被覆盖。



### LocalStorageLink

如果我们需要将自定义组件的状态变量的更新同步回LocalStorage，就需要用到@LocalStorageLink。

@LocalStorageLink(key)是和LocalStorage中key对应的属性建立双向数据同步：

1. 本地修改发生，该修改会被写回LocalStorage中；
2. LocalStorage中的修改发生后，该修改会被同步到所有绑定LocalStorage对应key的属性上，包括单向（@LocalStorageProp和通过prop创建的单向绑定变量）、双向（@LocalStorageLink和通过link创建的双向绑定变量）变量。

#### 装饰器使用规则说明

| @LocalStorageLink变量装饰器 | 说明                                                         |
| :-------------------------- | :----------------------------------------------------------- |
| 装饰器参数                  | key：常量字符串，必填（字符串需要有引号）。                  |
| 允许装饰的变量类型          | Object、class、string、number、boolean、enum类型，以及这些类型的数组。嵌套类型的场景请参考[观察变化和行为表现](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-localstorage.md#观察变化和行为表现)。 类型必须被指定，建议和LocalStorage中对应属性类型相同，否则会发生类型隐式转换，从而导致应用行为异常。不支持any，不允许使用undefined和null。 |
| 同步类型                    | **双向**同步：从LocalStorage的对应属性到自定义组件，从自定义组件到LocalStorage对应属性。 |
| 被装饰变量的初始值          | **必须指定**，如果LocalStorage实例中不存在属性，则作为初始化默认值，并存入LocalStorage中。 |

#### 变量的传递/访问规则说明

| 传递/访问            | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父节点初始化和更新 | 禁止，@LocalStorageLink不支持从父节点初始化，只能从LocalStorage中key对应的属性初始化，如果没有对应key的话，将使用本地默认值初始化。 |
| 初始化子节点         | 支持，可用于初始化@State、@Link、@Prop、@Provide。           |
| 是否支持组件外访问   | 否。                                                         |

 @LocalStorageLink初始化规则图示

![image-20240305161326698](./ArkTS.assets/image-20240305161326698.png)

#### 观察变化和行为表现

##### 观察变化

- 当装饰的数据类型为boolean、string、number类型时，可以观察到数值的变化。
- 当装饰的数据类型为class或者Object时，可以观察到赋值和属性赋值的变化，即Object.keys(observedObject)返回的所有属性。
- 当装饰的对象是array时，可以观察到数组添加、删除、更新数组单元的变化。

##### 框架行为

1. 当@LocalStorageLink(key)装饰的数值改变被观察到时，修改将被同步回LocalStorage对应属性键值key的属性中。
2. LocalStorage中属性键值key对应的数据一旦改变，属性键值key绑定的所有的数据（包括双向@LocalStorageLink和单向@LocalStorageProp）都将同步修改。
3. 当@LocalStorageLink(key)装饰的数据本身是状态变量，它的改变不仅仅会同步回LocalStorage中，还会引起所属的自定义组件的重新渲染。



### 使用场景

#### 应用逻辑使用LocalStorage

```ts
let para: Record<string,number> = { 'PropA': 47 };
let storage: LocalStorage = new LocalStorage(para); // 创建新实例并使用给定对象初始化
let propA: number | undefined = storage.get('PropA') // propA == 47
let link1: SubscribedAbstractProperty<number> = storage.link('PropA'); // link1.get() == 47
let link2: SubscribedAbstractProperty<number> = storage.link('PropA'); // link2.get() == 47
let prop: SubscribedAbstractProperty<number> = storage.prop('PropA'); // prop.get() == 47
link1.set(48); // two-way sync: link1.get() == link2.get() == prop.get() == 48
prop.set(1); // one-way sync: prop.get() == 1; but link1.get() == link2.get() == 48
link1.set(49); // two-way sync: link1.get() == link2.get() == prop.get() == 49
```

#### 从UI内部使用LocalStorage

除了应用程序逻辑使用LocalStorage，还可以借助LocalStorage相关的两个装饰器@LocalStorageProp和@LocalStorageLink，在UI组件内部获取到LocalStorage实例中存储的状态变量。

本示例以@LocalStorageLink为例，展示了：

- 使用构造函数创建LocalStorage实例storage；
- 使用@Entry装饰器将storage添加到CompA顶层组件中；
- @LocalStorageLink绑定LocalStorage对给定的属性，建立双向数据同步。

```ts
// 创建新实例并使用给定对象初始化
let para: Record<string, number> = { 'PropA': 47 };
let storage: LocalStorage = new LocalStorage(para);

@Component
struct Child {
 // @LocalStorageLink变量装饰器与LocalStorage中的'PropA'属性建立双向绑定
 @LocalStorageLink('PropA') storageLink2: number = 1;

 build() {
   Button(`Child from LocalStorage ${this.storageLink2}`)
     // 更改将同步至LocalStorage中的'PropA'以及Parent.storageLink1
     .onClick(() => {
       this.storageLink2 += 1
     })
 }
}
// 使LocalStorage可从@Component组件访问
@Entry(storage)
@Component
struct CompA {
 // @LocalStorageLink变量装饰器与LocalStorage中的'PropA'属性建立双向绑定
 @LocalStorageLink('PropA') storageLink1: number = 1;

 build() {
   Column({ space: 15 }) {
     Button(`Parent from LocalStorage ${this.storageLink1}`) // initial value from LocalStorage will be 47, because 'PropA' initialized already
       .onClick(() => {
         this.storageLink1 += 1
       })
     // @Component子组件自动获得对CompA LocalStorage实例的访问权限。
     Child()
   }
 }
}
```

#### @LocalStorageProp和LocalStorage单向同步的简单场景

在下面的示例中，CompA 组件和Child组件分别在本地创建了与storage的’PropA’对应属性的单向同步的数据，我们可以看到：

- CompA中对this.storProp1的修改，只会在CompA中生效，并没有同步回storage；
- Child组件中，Text绑定的storProp2 依旧显示47。

```ts
// 创建新实例并使用给定对象初始化
let para: Record<string, number> = { 'PropA': 47 };
let storage: LocalStorage = new LocalStorage(para);
// 使LocalStorage可从@Component组件访问
@Entry(storage)
@Component
struct CompA {
  // @LocalStorageProp变量装饰器与LocalStorage中的'PropA'属性建立单向绑定
  @LocalStorageProp('PropA') storageProp1: number = 1;

  build() {
    Column({ space: 15 }) {
      // 点击后从47开始加1，只改变当前组件显示的storageProp1，不会同步到LocalStorage中
      Button(`Parent from LocalStorage ${this.storageProp1}`)
        .onClick(() => {
          this.storageProp1 += 1
        })
      Child()
    }
  }
}

@Component
struct Child {
  // @LocalStorageProp变量装饰器与LocalStorage中的'PropA'属性建立单向绑定
  @LocalStorageProp('PropA') storageProp2: number = 2;

  build() {
    Column({ space: 15 }) {
      // 当CompA改变时，当前storageProp2不会改变，显示47
      Text(`Parent from LocalStorage ${this.storageProp2}`)
    }
  }
}
```

#### @LocalStorageLink和LocalStorage双向同步的简单场景

下面的示例展示了@LocalStorageLink装饰的数据和LocalStorage双向同步的场景：

```ts
// 构造LocalStorage实例
let para: Record<string, number> = { 'PropA': 47 };
let storage: LocalStorage = new LocalStorage(para);
// 调用link（api9以上）接口构造'PropA'的双向同步数据，linkToPropA 是全局变量
let linkToPropA: SubscribedAbstractProperty<object> = storage.link('PropA');

@Entry(storage)
@Component
struct CompA {

  // @LocalStorageLink('PropA')在CompA自定义组件中创建'PropA'的双向同步数据，初始值为47，因为在构造LocalStorage已经给“PropA”设置47
  @LocalStorageLink('PropA') storageLink: number = 1;

  build() {
    Column() {
      Text(`incr @LocalStorageLink variable`)
        // 点击“incr @LocalStorageLink variable”，this.storageLink加1，改变同步回storage，全局变量linkToPropA也会同步改变

        .onClick(() => {
          this.storageLink += 1
        })

      // 并不建议在组件内使用全局变量linkToPropA.get()，因为可能会有生命周期不同引起的错误。
      Text(`@LocalStorageLink: ${this.storageLink} - linkToPropA: ${linkToPropA.get()}`)
    }
  }
}
```

#### 兄弟组件之间同步状态变量

下面的示例展示了通过@LocalStorageLink双向同步兄弟组件之间的状态。

先看Parent自定义组件中发生的变化：

1. 点击“playCount ${this.playCount} dec by 1”，this.playCount减1，修改同步回LocalStorage中，Child组件中的playCountLink绑定的组件会同步刷新；
2. 点击“countStorage ${this.playCount} incr by 1”，调用LocalStorage的set接口，更新LocalStorage中“countStorage”对应的属性，Child组件中的playCountLink绑定的组件会同步刷新；
3. Text组件“playCount in LocalStorage for debug ${storage.get<number>(‘countStorage’)}”没有同步刷新，因为storage.get<number>(‘countStorage’)返回的是常规变量，常规变量的更新并不会引起Text组件的重新渲染。

Child自定义组件中的变化：

1. playCountLink的刷新会同步回LocalStorage，并且引起兄弟组件和父组件相应的刷新。

```ts
let ls: Record<string, number> = { 'countStorage': 1 }
let storage: LocalStorage = new LocalStorage(ls);

@Component
struct Child {
  // 子组件实例的名字
  label: string = 'no name';
  // 和LocalStorage中“countStorage”的双向绑定数据
  @LocalStorageLink('countStorage') playCountLink: number = 0;

  build() {
    Row() {
      Text(this.label)
        .width(50).height(60).fontSize(12)
      Text(`playCountLink ${this.playCountLink}: inc by 1`)
        .onClick(() => {
          this.playCountLink += 1;
        })
        .width(200).height(60).fontSize(12)
    }.width(300).height(60)
  }
}

@Entry(storage)
@Component
struct Parent {
  @LocalStorageLink('countStorage') playCount: number = 0;

  build() {
    Column() {
      Row() {
        Text('Parent')
          .width(50).height(60).fontSize(12)
        Text(`playCount ${this.playCount} dec by 1`)
          .onClick(() => {
            this.playCount -= 1;
          })
          .width(250).height(60).fontSize(12)
      }.width(300).height(60)

      Row() {
        Text('LocalStorage')
          .width(50).height(60).fontSize(12)
        Text(`countStorage ${this.playCount} incr by 1`)
          .onClick(() => {
            storage.set<number | undefined>('countStorage', Number(storage.get<number>('countStorage')) + 1);
          })
          .width(250).height(60).fontSize(12)
      }.width(300).height(60)

      Child({ label: 'ChildA' })
      Child({ label: 'ChildB' })

      Text(`playCount in LocalStorage for debug ${storage.get<number>('countStorage')}`)
        .width(300).height(60).fontSize(12)
    }
  }
}
```

#### 将LocalStorage实例从UIAbility共享到一个或多个视图

上面的实例中，LocalStorage的实例仅仅在一个@Entry装饰的组件和其所属的子组件（一个页面）中共享，如果希望其在多个视图中共享，可以在所属UIAbility中创建LocalStorage实例，并调用windowStage.[loadContent](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/apis/js-apis-window.md#loadcontent9)。

```ts
// EntryAbility.ts
import UIAbility from '@ohos.app.ability.UIAbility';
import window from '@ohos.window';

export default class EntryAbility extends UIAbility {
    para:Record<string, number> = { 'PropA': 47 };
    storage: LocalStorage = new LocalStorage(this.para);

    onWindowStageCreate(windowStage: window.WindowStage) {
        windowStage.loadContent('pages/Index', this.storage);
    }
}
```

> **说明：**
>
> 在UI页面通过getShared接口获取通过loadContent共享的LocalStorage实例。
>
> LocalStorage.getShared()只在模拟器或者实机上才有效，在Previewer预览器中使用不生效。

在下面的用例中，Index页面中的propA通过getShared()方法获取到共享的LocalStorage实例。点击Button跳转到Page页面，点击Change propA改变propA的值，back回Index页面后，页面中propA的值也同步修改。

```ts
// index.ets
import router from '@ohos.router';

// 通过getShared接口获取stage共享的LocalStorage实例
let storage = LocalStorage.getShared()

@Entry(storage)
@Component
struct Index {
  // can access LocalStorage instance using 
  // @LocalStorageLink/Prop decorated variables
  @LocalStorageLink('PropA') propA: number = 1;

  build() {
    Row() {
      Column() {
        Text(`${this.propA}`)
          .fontSize(50)
          .fontWeight(FontWeight.Bold)
        Button("To Page")
          .onClick(() => {
            router.pushUrl({
              url: 'pages/Page'
            })
          })
      }
      .width('100%')
    }
    .height('100%')
  }
}
ts
// Page.ets
import router from '@ohos.router';

let storage = LocalStorage.getShared()

@Entry(storage)
@Component
struct Page {
  @LocalStorageLink('PropA') propA: number = 2;

  build() {
    Row() {
      Column() {
        Text(`${this.propA}`)
          .fontSize(50)
          .fontWeight(FontWeight.Bold)

        Button("Change propA")
          .onClick(() => {
            this.propA = 100;
          })

        Button("Back Index")
          .onClick(() => {
            router.back()
          })
      }
      .width('100%')
    }
  }
}
```

> **说明：**
>
> 对于开发者更建议使用这个方式来构建LocalStorage的实例，并且在创建LocalStorage实例的时候就写入默认值，因为默认值可以作为运行异常的备份，也可以用作页面的单元测试。



## AppStorage：应用全局的UI状态存储

AppStorage是应用全局的UI状态存储，是和应用的进程绑定的，由UI框架在应用程序启动时创建，为应用程序UI状态属性提供中央存储。

和AppStorage不同的是，LocalStorage是页面级的，通常应用于页面内的数据共享。而AppStorage是应用级的全局状态共享，还相当于整个应用的“中枢”，[持久化数据PersistentStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md)和[环境变量Environment](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-environment.md)**都是通过AppStorage中转**，才可以和UI交互。



### 概述

AppStorage是在应用启动的时候会被创建的单例。他的目的是为了提供应用状态数据的中心存储，这些状态数据在应用级别都是可访问的。AppStorage将在应用运行过程保留期属性。属性通过唯一的键字符串值访问。

AppStorage可以和UI组件同步，且可以在应用业务逻辑中被访问。

AppStorage支持应用的[主线程](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/application-models/thread-model-stage.md)内多个UIAbility实例间的状态共享。

AppStorage中的属性可以被双向同步，数据可以是存在于本地或远程设备上，并具有不同的功能，比如数据持久化（详见[PersistentStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md)）。这些数据是通过业务逻辑中实现，与UI解耦，如果希望这些数据在UI中使用，需要用到[@StorageProp](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storageprop)和[@StorageLink](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#storagelink)。

### @StorageProp

在上文中已经提到，如果要建立AppStorage和自定义组件的联系，需要使用@StorageProp和@StorageLink装饰器。使用@StorageProp(key)/@StorageLink(key)装饰组件内的变量，key标识了AppStorage的属性。

**当自定义组件初始化的时候，会使用AppStorage中对应key的属性值将@StorageProp(key)/@StorageLink(key)装饰的变量初始化**。由于应用逻辑的差异，无法确认是否在组件初始化之前向AppStorage实例中存入了对应的属性，所以**AppStorage不一定存在key对应的属性**，**因此@StorageProp(key)/@StorageLink(key)装饰的变量进行本地初始化是必要的**。

@StorageProp(key)是和AppStorage中key对应的属性建立单向数据同步，允许本地改变，但是对于@StorageProp，本地的修改永远不会同步回AppStorage中，相反，如果AppStorage给定key的属性发生改变，改变会被同步给@StorageProp，并覆盖掉本地的修改。

#### 装饰器使用规则说明

| @StorageProp变量装饰器 | 说明                                                         |
| :--------------------- | :----------------------------------------------------------- |
| 装饰器参数             | key：常量字符串，必填（字符串需要有引号）。                  |
| 允许装饰的变量类型     | Object、 class、string、number、boolean、enum类型，以及这些类型的数组。嵌套类型的场景请参考[观察变化和行为表现](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#观察变化和行为表现)。 类型必须被指定，建议和AppStorage中对应属性类型相同，否则会发生类型隐式转换，从而导致应用行为异常。不支持any，不允许使用undefined和null。 |
| 同步类型               | 单向同步：从AppStorage的对应属性到组件的状态变量。 组件本地的修改是允许的，但是AppStorage中给定的属性一旦发生变化，将覆盖本地的修改。 |
| 被装饰变量的初始值     | **必须指定**，如果AppStorage实例中不存在属性，则作为初始化默认值，并存入AppStorage中。 |

#### 变量的传递/访问规则说明

| 传递/访问            | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父节点初始化和更新 | 禁止，@StorageProp不支持从父节点初始化，只能AppStorage中key对应的属性初始化，如果没有对应key的话，将使用本地默认值初始化 |
| 初始化子节点         | 支持，可用于初始化@State、@Link、@Prop、@Provide。           |
| 是否支持组件外访问   | 否。                                                         |

@StorageProp初始化规则图示

![image-20240306094848960](./ArkTS.assets/image-20240306094848960.png)

#### 观察变化和行为表现

##### 观察变化

- 当装饰的数据类型为boolean、string、number类型时，可以观察到数值的变化。
- 当装饰的数据类型为class或者Object时，可以观察到赋值和属性赋值的变化，即Object.keys(observedObject)返回的所有属性。
- 当装饰的对象是array时，可以观察到数组添加、删除、更新数组单元的变化。

##### 框架行为

- 当@StorageProp(key)装饰的数值改变被观察到时，修改不会被同步回AppStorage对应属性键值key的属性中。
- 当前@StorageProp(key)单向绑定的数据会被修改，即仅限于当前组件的私有成员变量改变，其他的绑定该key的数据不会同步改变。
- 当@StorageProp(key)装饰的数据本身是状态变量，它的改变虽然不会同步回AppStorage中，但是会引起所属的自定义组件的重新渲染。
- 当AppStorage中key对应的属性发生改变时，会同步给所有@StorageProp(key)装饰的数据，@StorageProp(key)本地的修改将被覆盖。

### @StorageLink

@StorageLink(key)是和AppStorage中key对应的属性建立双向数据同步：

1. 本地修改发生，该修改会被写回AppStorage中；
2. AppStorage中的修改发生后，该修改会被同步到所有绑定AppStorage对应key的属性上，包括单向（@StorageProp和通过Prop创建的单向绑定变量）、双向（@StorageLink和通过Link创建的双向绑定变量）变量和其他实例（比如PersistentStorage）。

#### 装饰器使用规则说明

| @StorageLink变量装饰器 | 说明                                                         |
| :--------------------- | :----------------------------------------------------------- |
| 装饰器参数             | key：常量字符串，必填（字符串需要有引号）。                  |
| 允许装饰的变量类型     | Object、class、string、number、boolean、enum类型，以及这些类型的数组。嵌套类型的场景请参考[观察变化和行为表现](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#观察变化和行为表现)。 类型必须被指定，建议和AppStorage中对应属性类型相同，否则会发生类型隐式转换，从而导致应用行为异常。不支持any，不允许使用undefined和null。 |
| 同步类型               | 双向同步：从AppStorage的对应属性到自定义组件，从自定义组件到AppStorage对应属性。 |
| 被装饰变量的初始值     | 必须指定，如果AppStorage实例中不存在属性，则作为初始化默认值，并存入AppStorage中。 |

#### 变量的传递/访问规则说明

| 传递/访问            | 说明                                                         |
| :------------------- | :----------------------------------------------------------- |
| 从父节点初始化和更新 | 禁止。                                                       |
| 初始化子节点         | 支持，可用于初始化常规变量、@State、@Link、@Prop、@Provide。 |
| 是否支持组件外访问   | 否。                                                         |

@StorageLink初始化规则图示

![image-20240306095526489](./ArkTS.assets/image-20240306095526489.png)

#### 观察变化和行为表现

##### 观察变化

- 当装饰的数据类型为boolean、string、number类型时，可以观察到数值的变化。
- 当装饰的数据类型为class或者Object时，可以观察到赋值和属性赋值的变化，即Object.keys(observedObject)返回的所有属性。
- 当装饰的对象是array时，可以观察到数组添加、删除、更新数组单元的变化。

##### 框架行为

1. 当@StorageLink(key)装饰的数值改变被观察到时，修改将被同步回AppStorage对应属性键值key的属性中。
2. AppStorage中属性键值key对应的数据一旦改变，属性键值key绑定的所有的数据（包括双向@StorageLink和单向@StorageProp）都将同步修改；
3. 当@StorageLink(key)装饰的数据本身是状态变量，它的改变不仅仅会同步回AppStorage中，还会引起所属的自定义组件的重新渲染。



###　使用场景

#### 从应用逻辑使用AppStorage和LocalStorage

**AppStorage是单例，它的所有API都是静态的**，使用方法类似于中LocalStorage对应的非静态方法。

```ts
AppStorage.setOrCreate('PropA', 47);

let storage: LocalStorage = new LocalStorage();
storage.setOrCreate('PropA',17);
let propA: number | undefined = AppStorage.get('PropA') // propA in AppStorage == 47, propA in LocalStorage == 17
let link1: SubscribedAbstractProperty<number> = AppStorage.link('PropA'); // link1.get() == 47
let link2: SubscribedAbstractProperty<number> = AppStorage.link('PropA'); // link2.get() == 47
let prop: SubscribedAbstractProperty<number> = AppStorage.prop('PropA'); // prop.get() == 47

link1.set(48); // two-way sync: link1.get() == link2.get() == prop.get() == 48
prop.set(1); // one-way sync: prop.get() == 1; but link1.get() == link2.get() == 48
link1.set(49); // two-way sync: link1.get() == link2.get() == prop.get() == 49

storage.get<number>('PropA') // == 17
storage.set('PropA', 101);
storage.get<number>('PropA') // == 101

AppStorage.get<number>('PropA') // == 49
link1.get() // == 49
link2.get() // == 49
prop.get() // == 49
```

#### 从UI内部使用AppStorage和LocalStorage

@StorageLink变量装饰器与AppStorage配合使用，正如@LocalStorageLink与LocalStorage配合使用一样。此装饰器使用AppStorage中的属性创建双向数据同步。

```ts
AppStorage.setOrCreate('PropA', 47);
let storage = new LocalStorage();
storage.setOrCreate('PropA', 48);

@Entry(storage)
@Component
struct CompA {
  @StorageLink('PropA') storageLink: number = 1;
  @LocalStorageLink('PropA') localStorageLink: number = 1;

  build() {
    Column({ space: 20 }) {
      Text(`From AppStorage ${this.storageLink}`)
        .onClick(() => {
          this.storageLink += 1
        })

      Text(`From LocalStorage ${this.localStorageLink}`)
        .onClick(() => {
          this.localStorageLink += 1
        })
    }
  }
}
```



#### 不建议借助@StorageLink的双向同步机制实现事件通知

不建议开发者使用@StorageLink和AppStorage的双向同步的机制来实现事件通知，因为AppStorage中的变量可能绑定在多个不同页面的组件中，但事件通知则不一定需要通知到所有的这些组件。并且，当这些@StorageLink装饰的变量在UI中使用时，会触发UI刷新，带来不必要的性能影响。



### 限制条件

AppStorage与[PersistentStorage](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md)以及[Environment](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-environment.md)配合使用时，需要注意以下几点：

- 在AppStorage中创建属性后，**调用PersistentStorage.persistProp()接口时，会使用在AppStorage中已经存在的值，并覆盖PersistentStorage中的同名属性，所以建议要使用相反的调用顺序**，反例可见[在PersistentStorage之前访问AppStorage中的属性](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-persiststorage.md#在persistentstorage之前访问appstorage中的属性)；
- 如果在AppStorage中已经创建属性后，**再调用Environment.envProp()创建同名的属性，会调用失败**。因为AppStorage**已经有同名属性**，Environment环境变量**不会再写入AppStorage中**，所以**建议AppStorage中属性不要使用Environment预置环境变量名**。
- 状态装饰器装饰的变量，改变会引起UI的渲染更新，如果改变的变量不是用于UI更新，**只是用于消息传递，推荐使用 `emitter`方式**。例子可见[不建议借助@StorageLink的双向同步机制实现事件通知](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-appstorage.md#不建议借助storagelink的双向同步机制实现事件通知)。



## PersistentStorage：持久化存储UI状态

前两个小节介绍的LocalStorage和AppStorage都是运行时的内存，但是在应用退出再次启动后，依然能保存选定的结果，是应用开发中十分常见的现象，这就需要用到PersistentStorage。

PersistentStorage是应用程序中的**可选单例对象**。此对象的作用是**持久化存储选定的AppStorage属性**，以确保这些属性在应用程序重新启动时的值与应用程序关闭时的值相同。

### 概述

PersistentStorage将选定的AppStorage属性保留在设备磁盘上。应用程序通过API，以决定哪些AppStorage属性应借助PersistentStorage持久化。UI和业务逻辑不直接访问PersistentStorage中的属性，所有属性访问都是对AppStorage的访问，**AppStorage中的更改会自动同步到PersistentStorage**。

PersistentStorage和AppStorage中的属性建立**双向同步**。应用开发通常通过AppStorage访问PersistentStorage，另外还有一些接口可以用于管理持久化属性，但是业务逻辑始终是通过AppStorage获取和设置属性的。



### 限制条件

PersistentStorage允许的类型和值有：

+ `number,string,boolean,enum`等简单类型
+ 可以被`JSON.stringify()`和`JSON.parse()`重构的对象。例如`Date,Map,Set`等内置类型则不支持，以及对象的属性方法不支持持久化。

PersistentStorage不允许的类型和值有：

+ 不支持嵌套对象（对象数组，对象的属性是对象等）。因为目前框架无法检测AppStorage中嵌套对象（包括数组）值的变化，所以无法写回到PersistentStorage中。
+ 不支持`undefined`和`null`

持久化数据是一个相对缓慢的操作，应用程序应避免以下情况：

- 持久化大型数据集。
- 持久化经常变化的变量。

PersistentStorage的持久化变量最好是小于2kb的数据，不要大量的数据持久化，因为PersistentStorage写入磁盘的操作是同步的，大量的数据本地化读写会同步在UI线程中执行，影响UI渲染性能。如果开发者需要存储大量的数据，建议使用数据库api。

PersistentStorage和UIContext相关联，需要在[UIContext](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/apis/js-apis-arkui-UIContext.md#uicontext)明确的时候才可以调用，可以通过在[runScopedTask](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/apis/js-apis-arkui-UIContext.md#runscopedtask)里明确上下文。如果没有在UIContext明确的地方调用，将导致无法持久化数据。

### 使用场景

#### 从AppStorage中访问PersistentStorage初始化的属性

1. 初始化PersistentStorage：

   ```ts
   PersistentStorage.persistProp('aProp', 47);
   ```

2. 在AppStorage获取对应属性：

   ```ts
   AppStorage.get<number>('aProp'); // returns 47
   ```

   或在组件内部定义：

   ```ts
   @StorageLink('aProp') aProp: number = 48;
   ```

完整代码如下：

```ts
PersistentStorage.persistProp('aProp', 47);

@Entry
@Component
struct Index {
  @State message: string = 'Hello World'
  @StorageLink('aProp') aProp: number = 48

  build() {
    Row() {
      Column() {
        Text(this.message)
        // 应用退出时会保存当前结果。重新启动后，会显示上一次的保存结果
        Text(`${this.aProp}`)
          .onClick(() => {
            this.aProp += 1;
          })
      }
    }
  }
}
```





## ForEach：循环渲染

ForEach接口基于数组类型数据来进行循环渲染，需要与容器组件配合使用，且接口返回的组件应当是允许包含在ForEach父容器组件中的子组件。例如，ListenItem组件要求ForEach的父容器组件必须为[List组件](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-container-list.md)。

### 接口描述

```ts
ForEach(
  arr: Array,
  itemGenerator: (item: any, index?: number) => void,
  keyGenerator?: (item: any, index?: number) => string
)
```

以下是参数的详细说明：

| 参数名        | 参数类型                                | 是否必填 | 参数描述                                                     |
| :------------ | :-------------------------------------- | :------- | :----------------------------------------------------------- |
| arr           | Array                                   | 是       | 数据源，为`Array`类型的数组。 <br />**说明：**<br /> - **可以设置为空数组，此时不会创建子组件**。<br /> - 可以设置返回值为数组类型的函数，例如`arr.slice(1, 3)`，但设置的函数不应改变包括数组本身在内的任何状态变量，例如不应使用`Array.splice()`,`Array.sort()`或`Array.reverse()`这些会改变原数组的函数。 |
| itemGenerator | `(item: any, index?: number) => void`   | 是       | 组件生成函数。 <br />- 为数组中的每个元素创建对应的组件。<br />- `item`参数：`arr`数组中的数据项。 <br />- `index`参数（可选）：`arr`数组中的数据项索引。 <br />**说明：** <br />- 组件的类型必须是`ForEach`的父容器所允许的。例如，`ListItem`组件要求`ForEach`的父容器组件必须为`List`组件。 |
| keyGenerator  | `(item: any, index?: number) => string` | 否       | 键值生成函数。 <br />- 为数据源`arr`的每个数组项生成唯一且持久的键值。函数返回值为开发者自定义的键值生成规则。 <br />- `item`参数：`arr`数组中的数据项。 <br />- `index`参数（可选）：`arr`数组中的数据项索引。 <br />**说明：** <br />- 如果函数缺省，框架默认的键值生成函数为`(item: T, index: number) => { return index + '__' + JSON.stringify(item); }` <br />- 键值生成函数不应改变任何组件状态。 |

> **说明：**
>
> - `ForEach`的`itemGenerator`函数可以包含`if/else`条件渲染逻辑。另外，也可以在`if/else`条件渲染语句中使用`ForEach`组件。
> - 在初始化渲染时，`ForEach`会加载数据源的所有数据，并为每个数据项创建对应的组件，然后将其挂载到渲染树上。如果数据源非常大或有特定的性能需求，建议使用`LazyForEach`组件。

### 键值生成规则

在`ForEach`循环渲染过程中，系统会为每个数组元素生成一个唯一且持久的键值，用于标识对应的组件。当这个键值变化时，ArkUI框架将视为该数组元素已被替换或修改，并会基于新的键值创建一个新的组件。

`ForEach`提供了一个名为`keyGenerator`的参数，这是一个函数，开发者可以通过它自定义键值的生成规则。如果开发者没有定义`keyGenerator`函数，则ArkUI框架会使用默认的键值生成函数，即`(item: any, index: number) => { return index + '__' + JSON.stringify(item); }`。

ArkUI框架对于`ForEach`的键值生成有一套特定的判断规则，这主要与`itemGenerator`函数的第二个参数`index`以及`keyGenerator`函数的第二个参数`index`有关，具体的键值生成规则判断逻辑如下图所示。

ForEach键值生成规则

![image-20240306113628353](./ArkTS.assets/image-20240306113628353.png)

> **说明：**
>
> ArkUI框架会**对重复的键值发出警告**。在UI更新的场景下，如果出现重复的键值，框架可能无法正常工作，具体请参见[渲染结果非预期](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md#渲染结果非预期)。



### 组件创建规则

在确定键值生成规则后，ForEach的第二个参数`itemGenerator`函数会根据键值生成规则为数据源的每个数组项创建组件。组件的创建包括两种情况：[ForEach首次渲染](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#首次渲染)和[ForEach非首次渲染](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#非首次渲染)。

#### 首次渲染

在ForEach首次渲染时，会根据前述键值生成规则为数据源的每个数组项生成唯一键值，并创建相应的组件

```ts
@Entry
@Component
struct Parent {
  @State simpleList: Array<string> = ['one', 'two', 'three'];

  build() {
    Row() {
      Column() {
        ForEach(this.simpleList, (item: string) => {
          ChildItem({ item: item })
        }, (item: string) => item)
      }
      .width('100%')
      .height('100%')
    }
    .height('100%')
    .backgroundColor(0xF1F3F5)
  }
}

@Component
struct ChildItem {
  @Prop item: string;

  build() {
    Text(this.item)
      .fontSize(50)
  }
}
```

在上述代码中，键值生成规则是`keyGenerator`函数的返回值`item`。在ForEach渲染循环时，为数据源数组项依次生成键值`one`、`two`和`three`，并创建对应的`ChildItem`组件渲染到界面上。

当不同数组项按照键值生成规则生成的键值相同时，框架的行为是未定义的。例如，在以下代码中，ForEach渲染相同的数据项`two`时，只创建了一个`ChildItem`组件，而没有创建多个具有相同键值的组件。

```ts
@Entry
@Component
struct Parent {
  @State simpleList: Array<string> = ['one', 'two', 'two', 'three'];

  build() {
    Row() {
      Column() {
        ForEach(this.simpleList, (item: string) => {
          ChildItem({ item: item })
        }, (item: string) => item)
      }
      .width('100%')
      .height('100%')
    }
    .height('100%')
    .backgroundColor(0xF1F3F5)
  }
}

@Component
struct ChildItem {
  @Prop item: string;

  build() {
    Text(this.item)
      .fontSize(50)
  }
}
```

在该示例中，最终键值生成规则为`item`。当ForEach遍历数据源`simpleList`，遍历到索引为1的`two`时，按照最终键值生成规则生成键值为`two`的组件并进行标记。当遍历到索引为2的`two`时，按照最终键值生成规则当前项的键值也为`two`，此时不再创建新的组件。



#### 非首次渲染

在ForEach组件进行非首次渲染时，它会检查新生成的键值是否在上次渲染中已经存在。**如果键值不存在，则会创建一个新的组件；如果键值存在，则不会创建新的组件，而是直接渲染该键值所对应的组件**。例如，在以下的代码示例中，通过点击事件修改了数组的第三项值为"new three"，这将触发ForEach组件进行非首次渲染。

```ts
@Entry
@Component
struct Parent {
  @State simpleList: Array<string> = ['one', 'two', 'three'];

  build() {
    Row() {
      Column() {
        Text('点击修改第3个数组项的值')
          .fontSize(24)
          .fontColor(Color.Red)
          .onClick(() => {
            this.simpleList[2] = 'new three';
          })

        ForEach(this.simpleList, (item: string) => {
          ChildItem({ item: item })
            .margin({ top: 20 })
        }, (item: string) => item)
      }
      .justifyContent(FlexAlign.Center)
      .width('100%')
      .height('100%')
    }
    .height('100%')
    .backgroundColor(0xF1F3F5)
  }
}

@Component
struct ChildItem {
  @Prop item: string;

  build() {
    Text(this.item)
      .fontSize(30)
  }
}
```

从本例可以看出`@State` 能够监听到简单数据类型数组数据源 `simpleList` 数组项的变化。

1. 当 `simpleList` 数组项发生变化时，会触发 `ForEach` 进行重新渲染。
2. `ForEach` 遍历新的数据源 `['one', 'two', 'new three']`，并生成对应的键值`one`、`two`和`new three`。
3. 其中，键值`one`和`two`在上次渲染中已经存在，所以 `ForEach` 复用了对应的组件并进行了渲染。对于第三个数组项 “new three”，由于其通过键值生成规则 `item` 生成的键值`new three`在上次渲染中不存在，因此 `ForEach` 为该数组项创建了一个新的组件。



### 使用场景

ForEach组件在开发过程中的主要应用场景包括：[数据源不变](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#数据源不变)、[数据源数组项发生变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#数据源数组项发生变化)（如插入、删除操作）、[数据源数组项子属性变化](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#数据源数组项子属性变化)。



### 不推荐案例

开发者在使用ForEach的过程中，若对于键值生成规则的理解不够充分，可能会出现错误的使用方式。错误使用一方面会导致功能层面问题，例如[渲染结果非预期](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#渲染结果非预期)，另一方面会导致性能层面问题，例如[渲染性能降低](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#渲染性能降低)。

#### 渲染结果非预期

在本示例中，通过设置`ForEach`的第三个参数`KeyGenerator`函数，自定义键值生成规则为数据源的索引`index`的字符串类型值。当点击父组件`Parent`中“在第1项后插入新项”文本组件后，界面会出现非预期的结果。

```ts
@Entry
@Component
struct Parent {
  @State simpleList: Array<string> = ['one', 'two', 'three'];

  build() {
    Column() {
      Button() {
        Text('在第1项后插入新项').fontSize(30)
      }
      .onClick(() => {
        this.simpleList.splice(1, 0, 'new item');
      })

      ForEach(this.simpleList, (item: string) => {
        ChildItem({ item: item })
      }, (item: string, index: number) => index.toString())
    }
    .justifyContent(FlexAlign.Center)
    .width('100%')
    .height('100%')
    .backgroundColor(0xF1F3F5)
  }
}

@Component
struct ChildItem {
  @Prop item: string;

  build() {
    Text(this.item)
      .fontSize(30)
  }
}
```

`ForEach`在首次渲染时，创建的键值依次为"0"、“1”、“2”。

插入新项后，数据源`simpleList`变为`['one', 'new item', 'two', 'three']`，框架监听到`@State`装饰的数据源长度变化触发`ForEach`重新渲染。

`ForEach`依次遍历新数据源，遍历数据项"one"时生成键值"0"，存在相同键值，因此不创建新组件。继续遍历数据项"new item"时生成键值"1"，存在相同键值，因此不创建新组件。继续遍历数据项"two"生成键值"2"，存在相同键值，因此不创建新组件。最后遍历数据项"three"时生成键值"3"，不存在相同键值，创建内容为"three"的新组件并渲染。

从以上可以看出，当最终键值生成规则包含`index`时，期望的界面渲染结果为`['one', 'new item', 'two', 'three']`，而实际的渲染结果为`['one', 'two', 'three', 'three']`，渲染结果不符合开发者预期。因此，开发者在使用`ForEach`时应尽量避免最终键值生成规则中包含`index`。

#### 渲染性能降低

在本示例中，`ForEach`的第三个参数`KeyGenerator`函数处于缺省状态。根据上述[键值生成规则](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-foreach.md/#键值生成规则)，此例使用框架默认的键值生成规则，即最终键值为字符串`index + '__' + JSON.stringify(item)`。当点击“在第1项后插入新项”文本组件后，`ForEach`将需要为第2个数组项以及其后的所有项重新创建组件。

```ts
@Entry
@Component
struct Parent {
  @State simpleList: Array<string> = ['one', 'two', 'three'];

  build() {
    Column() {
      Button() {
        Text('在第1项后插入新项').fontSize(30)
      }
      .onClick(() => {
        this.simpleList.splice(1, 0, 'new item');
        console.log(`[onClick]: simpleList is ${JSON.stringify(this.simpleList)}`);
      })

      ForEach(this.simpleList, (item: string) => {
        ChildItem({ item: item })
      })
    }
    .justifyContent(FlexAlign.Center)
    .width('100%')
    .height('100%')
    .backgroundColor(0xF1F3F5)
  }
}

@Component
struct ChildItem {
  @Prop item: string;

  aboutToAppear() {
    console.log(`[aboutToAppear]: item is ${this.item}`);
  }

  build() {
    Text(this.item)
      .fontSize(50)
  }
}
```

插入新项后，`ForEach`为`new item`、 `two`、 `three`三个数组项创建了对应的组件`ChildItem`，并执行了组件的[`aboutToAppear()`](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-custom-component-lifecycle.md#abouttoappear)生命周期函数。这是因为：

1. 在`ForEach`首次渲染时，创建的键值依次为`0__one`、`1__two`、`2__three`。
2. 插入新项后，数据源`simpleList`变为`['one', 'new item', 'two', 'three']`，ArkUI框架监听到`@State`装饰的数据源长度变化触发`ForEach`重新渲染。
3. `ForEach`依次遍历新数据源，遍历数据项`one`时生成键值`0__one`，键值已存在，因此不创建新组件。继续遍历数据项`new item`时生成键值`1__new item`，不存在相同键值，创建内容为`new item`的新组件并渲染。继续遍历数据项`two`生成键值`2__two`，不存在相同键值，创建内容为`two`的新组件并渲染。最后遍历数据项`three`时生成键值`3__three`，不存在相同键值，创建内容为`three`的新组件并渲染。

尽管此示例中界面渲染的结果符合预期，但每次插入一条新数组项时，`ForEach`都会为从该数组项起后面的所有数组项全部重新创建组件。当数据源数据量较大或组件结构复杂时，由于组件无法得到复用，将导致性能体验不佳。因此，**除非必要，否则不推荐将第三个参数`KeyGenerator`函数处于缺省状态，以及在键值生成规则中包含数据项索引`index`。**



## LazyForEach：数据懒加载

LazyForEach从提供的数据源中按需迭代数据，并在每次迭代过程中创建相应的组件。当在滚动容器中使用了LazyForEach，框架会根据滚动容器可视区域按需创建组件，当组件画出可视区域外时，框架会进行销毁回收以降低内存占用。

### 接口描述

```ts
LazyForEach(
    dataSource: IDataSource,             // 需要进行数据迭代的数据源
    itemGenerator: (item: any, index?: number) => void,  // 子组件生成函数
    keyGenerator?: (item: any, index?: number) => string // 键值生成函数
): void
```

**参数：**

| 参数名        | 参数类型                                                     | 必填 | 参数描述                                                     |
| :------------ | :----------------------------------------------------------- | :--- | :----------------------------------------------------------- |
| dataSource    | [IDataSource](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-lazyforeach.md#idatasource类型说明) | 是   | LazyForEach数据源，需要开发者实现相关接口。                  |
| itemGenerator | (item: any， index?:number) => void                          | 是   | 子组件生成函数，为数组中的每一个数据项创建一个子组件。 **说明：** item是当前数据项，index是数据项索引值。 itemGenerator的函数体必须使用大括号{…}。itemGenerator每次迭代只能并且必须生成一个子组件。itemGenerator中可以使用if语句，但是必须保证if语句每个分支都会创建一个相同类型的子组件。itemGenerator中不允许使用ForEach和LazyForEach语句。 |
| keyGenerator  | (item: any, index?:number) => string                         | 否   | 键值生成函数，用于给数据源中的每一个数据项生成唯一且固定的键值。当数据项在数组中的位置更改时，其键值不得更改，当数组中的数据项被新项替换时，被替换项的键值和新项的键值必须不同。键值生成器的功能是可选的，但是，为了使开发框架能够更好地识别数组更改，提高性能，建议提供。如将数组反向时，如果没有提供键值生成器，则LazyForEach中的所有节点都将重建。 **说明：** item是当前数据项，index是数据项索引值。 数据源中的每一个数据项生成的键值不能重复。 |



### IDataSource类型说明

```ts
interface IDataSource {
    totalCount(): number; // 获得数据总数
    getData(index: number): Object; // 获取索引值对应的数据
    registerDataChangeListener(listener: DataChangeListener): void; // 注册数据改变的监听器
    unregisterDataChangeListener(listener: DataChangeListener): void; // 注销数据改变的监听器
}
```

| 接口声明                                                     | 参数类型                                                     | 说明                                                      |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------------------------------------------------- |
| totalCount(): number                                         | -                                                            | 获得数据总数。                                            |
| getData(index: number): any                                  | number                                                       | 获取索引值index对应的数据。 index：获取数据对应的索引值。 |
| registerDataChangeListener(listener:[DataChangeListener](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-lazyforeach.md#datachangelistener类型说明)): void | [DataChangeListener](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-lazyforeach.md#datachangelistener类型说明) | 注册数据改变的监听器。 listener：数据变化监听器           |
| unregisterDataChangeListener(listener:[DataChangeListener](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-lazyforeach.md#datachangelistener类型说明)): void | [DataChangeListener](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-rendering-control-lazyforeach.md#datachangelistener类型说明) | 注销数据改变的监听器。 listener：数据变化监听器           |

### DataChangeListener类型说明

```ts
interface DataChangeListener {
    onDataReloaded(): void; // 重新加载数据完成后调用
    onDataAdded(index: number): void; // 添加数据完成后调用
    onDataMoved(from: number, to: number): void; // 数据移动起始位置与数据移动目标位置交换完成后调用
    onDataDeleted(index: number): void; // 删除数据完成后调用
    onDataChanged(index: number): void; // 改变数据完成后调用
    onDataAdd(index: number): void; // 添加数据完成后调用
    onDataMove(from: number, to: number): void; // 数据移动起始位置与数据移动目标位置交换完成后调用
    onDataDelete(index: number): void; // 删除数据完成后调用
    onDataChange(index: number): void; // 改变数据完成后调用
}
```

| 接口声明                                                | 参数类型                 | 说明                                                         |
| :------------------------------------------------------ | :----------------------- | :----------------------------------------------------------- |
| onDataReloaded(): void                                  | -                        | 通知组件重新加载所有数据。 键值没有变化的数据项会使用原先的子组件，键值发生变化的会重建子组件。 |
| onDataAdd(index: number): void8+                        | number                   | 通知组件index的位置有数据添加。 index：数据添加位置的索引值。 |
| onDataMove(from: number, to: number): void8+            | from: number, to: number | 通知组件数据有移动。 from: 数据移动起始位置，to: 数据移动目标位置。 **说明：** 数据移动前后键值要保持不变，如果键值有变化，应使用删除数据和新增数据接口。 |
| onDataDelete(index: number):void8+                      | number                   | 通知组件删除index位置的数据并刷新LazyForEach的展示内容。 index：数据删除位置的索引值。 **说明：** 需要保证dataSource中的对应数据已经在调用onDataDelete前删除，否则页面渲染将出现未定义的行为。 |
| onDataChange(index: number): void8+                     | number                   | 通知组件index的位置有数据有变化。 index：数据变化位置的索引值。 |
| onDataAdded(index: number):void(deprecated)             | number                   | 通知组件index的位置有数据添加。 从API 8开始，建议使用onDataAdd。 index：数据添加位置的索引值。 |
| onDataMoved(from: number, to: number): void(deprecated) | from: number, to: number | 通知组件数据有移动。 从API 8开始，建议使用onDataMove。 from: 数据移动起始位置，to: 数据移动目标位置。 将from和to位置的数据进行交换。 **说明：** 数据移动前后键值要保持不变，如果键值有变化，应使用删除数据和新增数据接口。 |
| onDataDeleted(index: number):void(deprecated)           | number                   | 通知组件删除index位置的数据并刷新LazyForEach的展示内容。 从API 8开始，建议使用onDataDelete。 index：数据删除位置的索引值。 |
| onDataChanged(index: number): void(deprecated)          | number                   | 通知组件index的位置有数据有变化。 从API 8开始，建议使用onDataChange。 index：数据变化监听器。 |

### 使用限制

- LazyForEach必须在容器组件内使用，仅有[List](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-container-list.md)、[Grid](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-container-grid.md)、[Swiper](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-container-swiper.md)以及[WaterFlow](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-container-waterflow.md)组件支持数据懒加载（可配置cachedCount属性，即只加载可视部分以及其前后少量数据用于缓冲），其他组件仍然是一次性加载所有的数据。
- LazyForEach在每次迭代中，必须创建且只允许创建一个子组件。
- 生成的子组件必须是允许包含在LazyForEach父容器组件中的子组件。
- 允许LazyForEach包含在if/else条件渲染语句中，也允许LazyForEach中出现if/else条件渲染语句。
- 键值生成器必须针对每个数据生成唯一的值，如果键值相同，将导致键值相同的UI组件被框架忽略，从而无法在父容器内显示。
- LazyForEach必须使用DataChangeListener对象来进行更新，第一个参数dataSource使用状态变量时，状态变量改变不会触发LazyForEach的UI刷新。
- 为了高性能渲染，通过DataChangeListener对象的onDataChange方法来更新UI时，需要生成不同于原来的键值来触发组件刷新。
