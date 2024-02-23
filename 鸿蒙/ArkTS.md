##　基本语法概述

在初步了解了ArkTS语言之后，我们以一个具体的示例来说明ArkTS的基本组成。如下图所示，当开发者点击按钮时，文本内容从“Hello World”变为“Hello ArkUI”。

ArkTS的基本组成

<img src="./ArkTS.assets/image-20240222170948974.png" alt="image-20240222170948974" style="zoom:50%;" />

>  **说明**：
>
> 自定义变量不能与基础通用属性/事件名重复

+ 装饰器：用于装饰类、结构、方法以及变量，并赋予其特殊的含义。如上述示例中`@Entry`、`@Component`和`@State`都是装饰器，[@Component](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/#自定义组件的基本结构)表示自定义组件，[@Entry](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/#自定义组件的基本结构)表示该自定义组件为入口组件，[@State](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-state.md/)表示组件中的状态变量，状态变量变化会触发UI刷新。
+ [UI描述](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-declarative-ui-description.md/)：以声明式的方式来描述UI的结构，例如build()方法中的代码块。
+ [自定义组件](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/quick-start/arkts-create-custom-components.md/)：可复用的UI单元，可组合其他组件，如上述被@Component装饰的struct Hello。
+ 系统组件：ArkUI框架中默认内置的基础和容器组件，可直接被开发者调用，比如示例中的Column、Text、Divider、Button。

- 属性方法：组件可以通过链式调用配置多项属性，如fontSize()、width()、height()、backgroundColor()等。

- 事件方法：组件可以通过链式调用设置多个事件的响应逻辑，如跟随在Button后面的onClick()。

- 系统组件、属性方法、事件方法具体使用可参考[基于ArkTS的声明式开发范式](https://docs.openharmony.cn/pages/v4.0/zh-cn/application-dev/reference/arkui-ts/ts-universal-events-click.md/)。