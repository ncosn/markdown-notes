# Google文档

### 创建目的地

#### 创建新的Activity目的地

用户导航到其他**Activity**，则当前的导航图不再位于作用域内，这意味着**Activity**目的地应被视为导航图中的一个端点。

如要添加**Activity**目的地，请使用其完全限定类名指定目的地**Activity**。

```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/navigation_graph"
    app:startDestination="@id/simpleFragment">

    <activity
        android:id="@+id/sampleActivityDestination"
        android:name="com.example.android.navigation.activity.DestinationActivity"
        android:label="@string/sampleActivityTitle" />
</navigation>
```

此XML相当于对`startActivity()`的调用

在某些情况下，您可能会发现此方法不适合。例如，您可能在 activity 类中未采用编译时依赖项，或者您可能会更倾向于使用运行某个隐式 intent 的间接层。目的地 `Activity` 的清单条目中的 [`intent-filter`](https://developer.android.google.cn/guide/topics/manifest/intent-filter-element?hl=zh-cn) 决定了您需要如何构造 `Activity` 目的地。

例如，请考虑使用以下清单文件：

···



### 设计导航图

#### 嵌套图

嵌套图表提供一定程度的封装，嵌套图表之外的目的地无法直接访问嵌套图表内的任务目的地。相反，这些目的地应**navigate()**到嵌套图表本身。

例如，假设您希望仅在用户首次启动应用时让其看到 **title_screen** 和 **register** 屏幕。之后，系统会存储用户信息。用户以后启动应用时，您应直接将其转到 **match** 屏幕。最佳实践应该是将 **match** 屏幕设置为顶级导航图的起始目的地，并将标题屏幕和注册屏幕移至嵌套图表中。

![img](https://developer.android.google.cn/static/images/topic/libraries/architecture/navigation-design-graph-nested.png?hl=zh-cn)

match 屏幕启动后，您可以检查是否有注册用户。如果用户未注册，您可以将其转到注册屏幕。如需详细了解条件导航情形，请参阅[条件导航](https://developer.android.google.cn/guide/navigation/navigation-conditional?hl=zh-cn)。

将图结构模块化的另一种方法是，通过父级导航图中的 `<include>` 元素，[将一个图包含在另一个图中](https://developer.android.google.cn/guide/navigation/navigation-nested-graphs?hl=zh-cn#include)。这样一来，就可以完全在单独的模块或项目中定义包含的图，从而最大限度提高可重用性。

#### 全局操作

对于应用中的任何可通过多条路径到达的目的地，都应定义一项可导航到该目的地的相应[全局操作](https://developer.android.google.cn/guide/navigation/navigation-global-action?hl=zh-cn)。全局操作可用于从任意位置导航到某目的地。

```xml
<!-- Action back to destination which launched into this in_game_nav_graph-->
   <action
           android:id="@+id/action_pop_out_of_game"
                       app:popUpTo="@id/in_game_nav_graph"
                       app:popUpToInclusive="true" />
```



### 转到目的地

导航到目的地是使用 [`NavController`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn) 完成的，它是一个在 `NavHost` 中管理应用导航的对象。每个 `NavHost` 均有自己的相应 `NavController`。`NavController` 提供了几种导航到目的地的不同方式，我们将在下文中进行详细说明。

如需检索 Fragment、Activity 或视图的 `NavController`，请使用以下某种方法：

**Kotlin**：

- [`Fragment.findNavController()`](https://developer.android.google.cn/reference/kotlin/androidx/navigation/fragment/package-summary?hl=zh-cn#findnavcontroller)
- [`View.findNavController()`](https://developer.android.google.cn/reference/kotlin/androidx/navigation/package-summary?hl=zh-cn#(android.view.View).findNavController())
- [`Activity.findNavController(viewId: Int)`](https://developer.android.google.cn/reference/kotlin/androidx/navigation/package-summary?hl=zh-cn#findnavcontroller)

**Java**：

- [`NavHostFragment.findNavController(Fragment)`](https://developer.android.google.cn/reference/androidx/navigation/fragment/NavHostFragment?hl=zh-cn#findNavController(androidx.fragment.app.Fragment))
- [`Navigation.findNavController(Activity, @IdRes int viewId)`](https://developer.android.google.cn/reference/androidx/navigation/Navigation?hl=zh-cn#findNavController(android.app.Activity, int))
- [`Navigation.findNavController(View)`](https://developer.android.google.cn/reference/androidx/navigation/Navigation?hl=zh-cn#findNavController(android.view.View))

检索 `NavController` 之后，您可以调用 [`navigate()`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#navigate(androidx.navigation.NavDirections)) 的某个重载，以在各个目的地之间导航。每个重载均支持多种导航场景，如以下部分所述。

#### 使用 ID 导航

[`navigate(int)`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#navigate(int)) 接受**操作(action)**或**目的地(fragm)**的**资源 ID** 作为参数。以下代码段展示了如何导航到 `ViewTransactionsFragment`：

```java
viewTransactionsButton.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Navigation.findNavController(view).navigate(R.id.viewTransactionsAction);
    }
});
```

#### 使用 DeepLinkRequest 导航

您可以使用 [`navigate(NavDeepLinkRequest)`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#navigate(androidx.navigation.NavDeepLinkRequest)) 直接导航到[隐式深层链接目的地](https://developer.android.google.cn/guide/navigation/navigation-deep-link?hl=zh-cn#implicit)，如下例所示：

```java
NavDeepLinkRequest request = NavDeepLinkRequest.Builder
    .fromUri(Uri.parse("android-app://androidx.navigation.app/profile"))
    .build()
NavHostFragment.findNavController(this).navigate(request)
```

···



#### 导航和返回堆栈

Android 会维护一个[返回堆栈](https://developer.android.google.cn/guide/components/activities/tasks-and-back-stack?hl=zh-cn)，其中包含您之前访问过的目的地。当用户打开您的应用时，应用的第一个目的地就放置在堆栈中。每次调用 [`navigate()`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#navigate(int)) 方法都会将另一目的地放置到堆栈的顶部。点按**向上**或**返回**会分别调用 [`NavController.navigateUp()`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#navigateUp()) 和 [`NavController.popBackStack()`](https://developer.android.google.cn/reference/androidx/navigation/NavController?hl=zh-cn#popBackStack()) 方法，用于移除（或弹出）堆栈顶部的目的地。

`NavController.popBackStack()` 会返回一个布尔值，表明它是否已成功返回到另一个目的地。当返回 `false` 时，最常见的情况是手动弹出图的起始目的地。

如果该方法返回 `false`，则 `NavController.getCurrentDestination()` 会返回 `null`。您应负责导航到新目的地，或通过对 Activity 调用 `finish()` 来处理弹出情况，如下例所示：

```java
...

if (!navController.popBackStack()) {
    // Call finish() on your Activity
    finish();
}
```



#### popUpTo 和 popUpToInclusive

使用操作进行导航时，您可以选择从返回堆栈上弹出其他目的地。例如，如果您的应用具有初始登录流程，那么在用户登录后，您应将所有与登录相关的目的地从返回堆栈上弹出，这样返回按钮就不会将用户带回登录流程。

如需在从一个目的地导航到另一个目的地时弹出目的地，请在关联的 `<action>` 元素中添加 `app:popUpTo` 属性。`app:popUpTo` 会告知 Navigation 库在调用 `navigate()` 的过程中从返回堆栈上弹出一些目的地。属性值是应保留在堆栈中的最新目的地的 ID。

您还可以添加 `app:popUpToInclusive="true"`，以表明在 `app:popUpTo` 中指定的目的地也应从返回堆栈中移除。

···

```xml
<fragment
    android:id="@+id/c"
    android:name="com.example.myapplication.C"
    android:label="fragment_c"
    tools:layout="@layout/fragment_c">

    <action
        android:id="@+id/action_c_to_a"
        app:destination="@id/a"
        app:popUpTo="@id/a"
        app:popUpToInclusive="true"/>
</fragment>
```

在到达目的地 C 之后，返回堆栈包含每个目的地（A、B 和 C）的一个实例。当返回到目的地 A 时，我们也 `popUpTo` A，也就是说我们会在导航过程中从堆栈中移除 B 和 C。利用 `app:popUpToInclusive="true"`，我们还会将第一个 A 从堆栈上弹出，从而有效地清除它。请注意，如果您不使用 `app:popUpToInclusive`，则返回堆栈会包含目的地 A 的两个实例



### 支持多个返回堆栈

Navigation 组件可与 Android 操作系统配合使用，以在用户在您的应用中导航时维护[返回堆栈](https://developer.android.google.cn/guide/navigation/navigation-navigate?hl=zh-cn#back-stack)。在某些情况下，同时维护多个返回堆栈可能很有帮助，用户可以在这些返回堆栈之间来回移动。例如，如果您的应用包含[底部导航栏](https://developer.android.google.cn/guide/navigation/navigation-ui?hl=zh-cn#bottom_navigation)或[抽屉式导航栏](https://developer.android.google.cn/guide/navigation/navigation-ui?hl=zh-cn#add_a_navigation_drawer)，支持多个返回堆栈可让您的用户在各个流程之间自由切换，同时不会在任何流程中丢失所处的位置。

Navigation 组件提供了支持多个返回堆栈的 API，这些 API 会在您的[导航图](https://developer.android.google.cn/guide/navigation/navigation-design-graph?hl=zh-cn)中保存和恢复目的地的状态。[`NavigationUI`](https://developer.android.google.cn/reference/androidx/navigation/ui/NavigationUI?hl=zh-cn) 类包含自动处理这种情况的方法，但您也可以手动使用底层 API 来获得自定义程度更高的实现。

#### 



### Anim

```java
Fragment fragment = new FragmentB();
getSupportFragmentManager().beginTransaction()
    .setCustomAnimations(
        R.anim.slide_in,  // enter
        R.anim.fade_out,  // exit
        R.anim.fade_in,   // popEnter
        R.anim.slide_out  // popExit
    )
    .replace(R.id.fragment_container, fragment)
    .addToBackStack(null)
    .commit();
```

