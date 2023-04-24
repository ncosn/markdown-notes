# Android的开发规范与开发工具

## 一.基本开发规范

### 1.命名规范

代码中的命名严禁使用拼音与英文混合的方式，更不允许直接使用中文的方式。正确的英文拼写和语法可以让阅读者易于理解，避免歧义。

#### 1.1 命名风格

* 包名:

  包名全部小写，连续的单词只是简单地连接起来，不使用下划线，采用反域名命名规则，全部使用小写字母。一级包名是顶级域名，通常为 `com`、`edu`、`gov`、`net`、`org` 等，二级包名为公司名，三级包名根据应用进行命名，例如云终端项目的包名为 com.sgcc. wsgw.cn

* 类名:类名都是以`UpperCamelCase` 风格编写,采用大驼峰命名法，尽量避免缩写，除非该缩写是众所周知的， 比如 HTML、URL，如果类名称中包含单词缩写，则单词缩写的每个字母均应大写.但以下情形例外：DO/BO/DTO/VO/AO/PO等.例如 WelcomeActivity NewsAdapter UserDto...

* 方法名:方法名都以 `lowerCamelCase` 风格编写,必须遵从驼峰形式。

* 常量名:常量名命名模式为 `CONSTANT_CASE`，全部字母大写，用下划线分隔单词。

#### 1.2 常量定义规范

* 尽量不要使用任何魔法值（即未经预先定义的常量）直接出现在代码中。

  反例:

  `if (key.equals("qwefrvb23gr34f")) {
              //...
      } `

  正例:

  ` String KEY_PRE = "qwefrvb23gr34f";
      if (KEY_PRE.equals(key)) {
              //...
      }`

* long 或者 Long 初始赋值时，使用大写的 L，不能是小写的 l，小写容易跟数字 1 混淆，造成误解。

* 不要使用一个常量类维护所有常量，按常量功能进行归类，分开维护。
  说明：大而全的常量类，非得使用查找功能才能定位到修改的常量，不利于理解和维护。
  正例：缓存相关常量放在类 CacheConsts 下；系统配置相关常量放在类 ConfigConsts 下。

### 2. 代码样式规范

#### 2.1 标准的大括号样式

左大括号不单独占一行，与其前面的代码位于同一行：

```java
class MyClass {
    int func() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }
}
```

其实有关条件语句的大括号只要通过Android Studio里的格式化代码快捷键就可以自动完成,但是需要注意的是,代码开发里不允许下面的样式出现:

```java
if (condition)
    body();
```

#### 2.2 换行相关

在单行代码出现过多字符数时,需要进行换行,换行时应该遵循以下原则:

1） 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例。
2） 运算符与下文一起换行。
3） 方法调用的点符号与下文一起换行。
4） 方法调用时，多个参数，需要换行时，在逗号后进行。
5） 在括号前不要换行，见反例。
正例：

```java
StringBuffer sb = new StringBuffer(); 
// 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行
sb.append("zi").append("xin")... 
   .append("huang")... 
   .append("huang")... 
   .append("huang");

```

反例：

```java
StringBuffer sb = new StringBuffer(); 
// 超过 120 个字符的情况下，不要在括号前换行
sb.append("zi").append("xin")...append 
    ("huang"); 
// 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行
method(args1, args2, args3, ... 
    , argsX); 
```

 #### 2.3 空格相关

* 左小括号和字符之间不出现空格；同样，右小括号和字符之间也不出现空格。
* if/for/while/switch/do 等保留字与括号之间都必须加空格。
* 采用 4 个空格缩进，如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。IDEA 设置 tab 为 4 个空格时,请勿勾选 Use tab character

### 3. 资源文件规范

资源文件命名为全部小写，采用下划线命名法。

#### 3.1 动画资源名称

动画文件需放在 `res/anim/` 目录下

命名规则：`{模块名_}逻辑名称`。

说明：`{}` 中的内容为可选，`逻辑名称` 可由多个单词加下划线组成。

例如：`refresh_progress.xml`、`market_cart_add.xml`、`market_cart_remove.xml`。

#### 3.2 颜色资源文件

专门存放颜色相关的资源文件。

命名规则：`类型_逻辑名称`。

例如：`sel_btn_font.xml`。

 #### 3.3 图片资源文件

`res/drawable/` 目录下放的是位图文件（.png、.9.png、.jpg、.gif）或编译为可绘制对象资源子类型的 XML 文件，而 `res/mipmap/` 目录下放的是不同密度的启动图标，所以 `res/mipmap/` 只用于存放启动图标，其余图片资源文件都应该放到 `res/drawable/` 目录下。

命名规则：`类型_逻辑名称`、`类型_颜色`。

例如：

| 名称                      | 说明                                |
| ------------------------- | ----------------------------------- |
| `btn_main_about.png`      | 主页关于按键 `类型_模块名_逻辑名称` |
| `btn_back.png`            | 返回按键 `类型_逻辑名称`            |
| `divider_maket_white.png` | 商城白色分割线 `类型_模块名_颜色`   |
| `ic_edit.png`             | 编辑图标 `类型_逻辑名称`            |
| `bg_main.png`             | 主页背景 `类型_逻辑名称`            |
| `btn_red.png`             | 红色按键 `类型_颜色`                |
| `btn_red_big.png`         | 红色大按键 `类型_颜色`              |
| `ic_head_small.png`       | 小头像图标 `类型_逻辑名称`          |
| `bg_input.png`            | 输入框背景 `类型_逻辑名称`          |

#### 3.4 布局资源文件

命名规则：`类型_模块名`

例如：

| 名称                        | 说明                                    |
| --------------------------- | --------------------------------------- |
| `activity_main.xml`         | 主窗体 `类型_模块名`                    |
| `activity_main_head.xml`    | 主窗体头部 `类型_模块名_逻辑名称`       |
| `fragment_music.xml`        | 音乐片段 `类型_模块名`                  |
| `fragment_music_player.xml` | 音乐片段的播放器 `类型_模块名_逻辑名称` |
| `dialog_loading.xml`        | 加载对话框 `类型_逻辑名称`              |
| `pop_info.xml`              | 信息弹窗（PopupWindow） `类型_逻辑名称` |
| `item_main_song.xml`        | 主页歌曲列表项 `类型_模块名_逻辑名称`   |

#### 3.5 values资源文件

`values/` 资源文件下的文件都以 `s` 结尾，如 `attrs.xml`、`colors.xml`、`dimens.xml`，起作用的不是文件名称，而是 `<resources>` 标签下的各种标签，比如 `<style>` 决定样式，`<color>` 决定颜色等

##### 3.5.1 colors.xml

`<color>` 的 `name` 命名使用下划线命名法，在你的 `colors.xml` 文件中应该只是映射颜色的名称一个 ARGB 值，而没有其它的。不要使用它为不同的按钮来定义 ARGB 值。例如:

```xml
<resources>

      <!-- grayscale -->
      <color name="white"     >#FFFFFF</color>
      <color name="gray_light">#DBDBDB</color>
      <color name="gray"      >#939393</color>
      <color name="gray_dark" >#5F5F5F</color>
      <color name="black"     >#323232</color>

      <!-- basic colors -->
      <color name="green">#27D34D</color>
      <color name="blue">#2A91BD</color>
      <color name="orange">#FF9D2F</color>
      <color name="red">#FF432F</color>

  </resources>
```



##### 3.5.2 dimens.xml

像对待 `colors.xml` 一样对待 `dimens.xml` 文件，与定义颜色调色板一样，你同时也应该定义一个空隙间隔和字体大小的“调色板”。 一个好的例子，如下所示：

```xml
<resources>

    <!-- font sizes -->
    <dimen name="font_22">22sp</dimen>
    <dimen name="font_18">18sp</dimen>
    <dimen name="font_15">15sp</dimen>
    <dimen name="font_12">12sp</dimen>

    <!-- typical spacing between two views -->
    <dimen name="spacing_40">40dp</dimen>
    <dimen name="spacing_24">24dp</dimen>
    <dimen name="spacing_14">14dp</dimen>
    <dimen name="spacing_10">10dp</dimen>
    <dimen name="spacing_4">4dp</dimen>

    <!-- typical sizes of views -->
    <dimen name="button_height_60">60dp</dimen>
    <dimen name="button_height_40">40dp</dimen>
    <dimen name="button_height_32">32dp</dimen>

</resources>
```

布局时在写 `margins` 和 `paddings` 时，你应该使用 `spacing_xx` 尺寸格式来布局，而不是像对待 `string` 字符串一样直接写值，像这样规范的尺寸很容易修改或重构，会使应用所有用到的尺寸一目了然。 这样写会非常有感觉，会使组织和改变风格或布局非常容易。



##### 3.5.3 strings.xml

`<string>` 的 `name` 命名使用下划线命名法，采用以下规则：`{模块名_}逻辑名称`，这样方便同一个界面的所有 `string` 都放到一起，方便查找。

| 名称                | 说明           |
| ------------------- | -------------- |
| `main_menu_about`   | 主菜单按键文字 |
| `friend_title`      | 好友模块标题栏 |
| `friend_dialog_del` | 好友删除提示   |
| `login_check_email` | 登录验证       |
| `dialog_title`      | 弹出框标题     |
| `button_ok`         | 确认键         |
| `loading`           | 加载文字       |

##### 3.5.4 styles.xml

<style> 的 name 命名使用大驼峰命名法，几乎每个项目都需要适当的使用 styles.xml 文件，因为对于一个视图来说，有一个重复的外观是很常见的，将所有的外观细节属性（colors、padding、font）放在 styles.xml 文件中。 在应用中对于大多数文本内容，最起码你应该有一个通用的 styles.xml 文件

  



### 4. 注释规范

为了其他人更好地读懂代码,我们需要在关键的地方进行代码的注释

#### 4.1 类注释

在Android Studio中可以在设置中进行注释的配置,通过进入 Settings -> Editor -> File and Code Templates -> Includes -> File Header，输入以下内容,就可以对每一个新建的类自动添加类注释

```java
/**
 *     author : ${USER}
 *     e-mail : xxx@xx
 *     time   : ${YEAR}/${MONTH}/${DAY}
 *     desc   :
 *     version: 1.0
 */

```





#### 4.2 方法注释

每一个自定义的方法,都应该加入对应的注释,同样在Android Studio中也可以设置相应的模版(Settings -> Keymap -> Fix doc comment）或者在方法前一行输入 `/** + 回车`就可以自动生成注释,我们只要更换对应的参数即可

```java
/**
 * 启动本地浏览器访问指定的网络地址
 *
 * @param httpUrl 网络地址
 */
public static void launchBrowser(String httpUrl) {
    if (StringUtil.notEmpty(httpUrl)) {
        Uri uriUrl = Uri.parse(httpUrl);
        Intent launchBrowser = new Intent(Intent.ACTION_VIEW, uriUrl);
        DataApplication.application.startActivity(launchBrowser);
    }
}
```

#### 4.3 块注释

如果要对某一段代码进行注释,则可以是 `/* ... */` 风格，也可以是 `// ...` 风格（**`//`后最好带一个空格**）

```java
/*
 * This is
 * okay.
 */

// And so
// is this.

/* Or you can
* even do this. */
```



### 5 其他

#### 5.1 Alibaba Java Coding Guidelines

这是一个可以自动判断并且能对已有的代码进行检测是否符合代码规范的工具,在Android Studio的plugin里可以进行查找并下载,这样在平常的开发中就可以避免一些不符合规范的代码出现,或者通过[这里](https://plugins.jetbrains.com/plugin/10046-alibaba-java-coding-guidelines)下载安装

#### 5.2  UI控件缩写表

在给一些空间的ID进行命名时,最好能够统一命名规范,下面是平常能用到的一些UI控件缩写表

```
名称	缩写
Button	btn
CheckBox	cb
ConstraintLayout cl
EditText	et
FrameLayout	fl
GridView	gv
ImageButton	ib
ImageView	iv
LinearLayout	ll
ListView	lv
ProgressBar	pb
RadioButtion	rb
RecyclerView	rv
RelativeLayout	rl
ScrollView	sv
SeekBar	sb
Spinner	spn
TextView	tv
ToggleButton	tb
VideoView	vv
WebView	wv

```

#### 5.3...

## 二.Git协作开发

现在主要开发的项目(云终端和移动终端)都已经通过git来进行版本控制,目前准备通过开发和生产两个分支,对项目进行协作开发.

#### 2.1 云终端

由于目前有两个在开发的版本:Ukey的能力开放平台和非Ukey的服务连接平台,所以会对应有四个分支

master			非Ukey正式:在正式发布版本后有dev分支合并而来,通常不会经常更新,较为稳定的代码

dev      			非Ukey开发:平常开发时的分支,注意在提交之前进行合并即可.

master-Ukey  Ukey正式:在正式发布版本后有dev分支合并而来,通常不会经常更新,较为稳定的代码

dev-Ukey		Ukey开发:平常开发时的分支,注意在提交之前进行合并即可.

#### 2.2 移动终端

目前只有一个开发的版本,所以只通过master和dev两个分支进行开发就可以了

master	在正式发布版本后有dev分支合并而来,通常不会经常更新,较为稳定的代码

dev      	平常开发时的分支,注意在提交之前进行合并即可.

#### 2.3 git操作流程

以一个简单的例子来展示一个通常的git操作流程

1. 在开发时切换到dev分支![image-20211008160949629](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211008160949629.png)
2. 开发完代码后在提交前点击更新代码按钮![image-20211008161031004](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211008161031004.png)
3. 更新完代码后选择需要提交的文件![image-20211008161530605](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211008161530605.png)
4. 填入提交的信息,点击Commit and Push来提交并push到dev分支上![image-20211008161626359](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211008161626359.png)
5. push完所有当前版本要提交的版本后checkout到master分支,选择dev->merge into current j将dev分支代码合并到master分支上,完成版本合并![image-20211008161936077](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\image-20211008161936077.png)

## 三. Jenkins持续集成

计划通过Jenkins实现云终端和移动终端的持续集成



