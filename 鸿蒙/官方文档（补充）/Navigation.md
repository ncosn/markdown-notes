### Navigation组件

包括**主页**和**内容页**

主页由**标题栏**、**内容区**和**工具栏**组成，内容区中使用`NavRouter`子组件实现导航栏功能。内容页主要显示 `NavDestination` 子组件中的内容。

NavRouter是配合Navigation使用的特殊子组件，**默认提供点击响应处理**，不需要开发者自定义点击事件逻辑。NavRouter有且仅有两个子组件，其中第二个子组件必须是NavDestination。NavDestination是配合NavRouter使用的特殊子组件，用于显示Navigation组件的内容页。**当开发者点击NavRouter组件时，会跳转到对应的NavDestination内容区**。



#### 页面显示模式

**`mode`**

+ 自适应模式：NavigationMode.Auto
+ 单页面模式：NavigationMode.Stack
+ 分栏模式：NavigaitionMode.Split



#### 设置标题栏模式

**`titleMode`**

+ Mini模式：NavigationTitleMode.Mini	普通型标题栏，用于一级页面不需要突出标题
+ Full模式：NavigationTitleMode.Full	强调性标题栏，用于一级页面突出标题



#### 设置菜单栏

**`menus`**

支持Array\<NavigationMenuItem>和CustomBuilder两种参数类型

```ts
let TooTmp: NavigationMenuItem = {'value': "", 'icon': "resources/base/media/ic_public_highlights.svg", 'action': ()=> {}}
Navigation() {
  ...
}
.menus([TooTmp,
  TooTmp,
  TooTmp])
```



#### 设置工具栏

**`toolbarConfiguration`**

```ts
let TooTmp: ToolbarItem = {'value': "func", 'icon': "./image/ic_public_highlights.svg", 'action': ()=> {}}
let TooBar: ToolbarItem[] = [TooTmp,TooTmp,TooTmp]
Navigation() {
  ...
}
.toolbarConfiguration(TooBar)
```



#### 其他属性

`hideToolBar`

隐藏工具栏



`hideTitleBar`

隐藏标题栏



`hideBackTitle`

隐藏标题栏中的返回键，不支持隐藏NavDestination组件标题栏中的返回键。

> 仅titleMode为Mini时生效



`navBarWidth`

导航栏宽度。

> 仅分栏时生效





### NavRouter（API 9）

**必须包含两个子组件**，其中第二个子组件必须为NavDestination

#### 属性

**`mode`**：指定点击NavRouter跳转到NavDestination页面时，使用的路由模式。

+ **NavRouteMode.PUSH_WITH_RECRETE**
  跳转到新的NavDestination页面时，替换当前显示的NavDestination页面，页面销毁，但该页面信息仍保留在路由栈中。
+ **NavRouteMode.PUSH**
  跳转到新的NavDestination页面时，覆盖当前显示的NavDestination页面，该页面不销毁，且页面信息保留在路由栈中。
+ **NavRouteMode.REPLACE**
  跳转到新的NavDestination页面时，替换当前显示的NavDestination页面，页面销毁，且该页面信息从路由栈中清除。



### NavPathStack（API 10推荐）

Navigation路由栈。



#### 绑定路由栈到Navigation组件

Navigation(pathInfos: NavPathStack)



#### `pushPath`

pushPath(info: **NavPathInfo**): void

将info指定的NavDestination页面信息入栈。

> **NavPathInfo**
>
> | 名称  | 类型    | 必填 | 描述                         |
> | :---- | :------ | :--- | :--------------------------- |
> | name  | string  | 是   | NavDestination页面名称。     |
> | param | unknown | 是   | NavDestination页面详细参数。 |



#### `pushPathByName`

pushPathByName(name: string, param: unknown): void

将name指定的NavDestination页面信息入栈，传递的数据为param。



#### `pop`

pop(): NavPathInfo | undefined

弹出路由栈栈顶元素。



#### `popToName`

popToName(name: string): number

回退路由栈到第一个名为name的NavDestination页面。



