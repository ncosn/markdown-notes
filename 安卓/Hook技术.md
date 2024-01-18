## 14 Hook技术

### 14.1 Hook技术概述

我们知道Android系统的代码调用和回调都是按照一定顺序执行的，这里举一个简单的例子，如图14-1所示、

<img src="./Hook技术.assets/image-20240118134803463.png" alt="image-20240118134803463" style="zoom:67%;" />

对象A调用类对象，对象B处理后将数据回调给对象A，接着来看采用Hook的调用流程，如图14-2所示。

<img src="./Hook技术.assets/image-20240118134859520.png" alt="image-20240118134859520" style="zoom:67%;" />

图14-2中的Hook可以是一个方法或者一个对象，它像一个钩子一样挂在对象A和对象B之间，当对象A调用对象B之前做一些处理（比如修改方法的参数和返回值），起到了“欺上瞒下”的作用，与其说Hook是钩子，不如说是劫持来的更贴切些。我们知道应用程序进程之间是彼此独立的，应用程序进程和系统进程之间也是如此，想要在应用程序进程更改系统进程的某些行为很难直接实现，有了Hook技术，我们就可以在进程间进行行为更改，如图14-3所示。

<img src="./Hook技术.assets/image-20240118135243392.png" alt="image-20240118135243392" style="zoom:67%;" />

可以看到Hook可以将自己融入到它所要劫持的对象（对象B）所在的进程中，成为系统进程的一部分，这样我们就可以通过Hook来更改对象B的行为。被劫持的对象（对象B），称作Hook点，为了保证Hook的稳定性，Hook点一般选择容易找到并且不易变化的对象，静态变量和单例就符合这一条件。



### 14.2 Hook技术分类

Hook技术知识点比较多，Hook技术根据不同的角度有很多分类，这里介绍其中三种分类。

根据Hook的API语言划分，分为Hook Java和Hook Native。

+ Hook Java主要通过反射和代理来实现，应用于在SDK开发环境中修改Java代码。
+ Hook Native则应用于NDK开发环境和系统开发中修改Native代码。

根据Hook的进程划分，分为应用程序进程Hook和全局Hook。

+ 应用程序进程Hook只能Hook当前所在的应用程序进程。
+ 应用程序进程是Zygotefock出来的，如果对Zygote进行Hook，就可以实现Hook系统所有的应用程序进程，这就是全局Hook。

根据Hook的实现方式划分，分为如下两种。

+ 通过反射和代码实现，只能Hook当前的应用程序进程。
+ 通过Hook框架来实现，比如Xposed，可以实现全局Hook，但是需要root。

Hook Native、全局Hook和通过Hook框架实现这些分类和插件化技术关联不大，本章主要需要学习的HookJava，想要更好学习Hook Java，首先了解代理模式。



### 14.3 代理模式





### 14.4 Hook startActivity方法

我们知道Hook可以用来劫持对象，被劫持的对象叫做Hook点，用代理对象来替代Hook点，这样我们就可以在代理上实现自己想做的操作。这里以Hook常用startActivity方法来举例，startActivity方法分为两个，如下所示：

```java
startActivity(intent);
getApplicationContext().startActivity(intent);
```

第一个是Activity的startActivity方法，第二个是Context的startActivity方法，这两个方法的调用链是不同的，这里分开进行讲解。

#### 14.4.1 Hook Activity的startActivity方法

Activity的startActivity方法在4.1.1中提到过，代码如下：

frameworks/base/core/java/android/app/Activity.java

```java
@Override
public void startActivity(Intent intent,@Nullable Bundle options) {
    if (options!=null){
        startActivityForResult(intent,-1,options);
    } else {
        startActivityForResult(intent,-1);
    }
}
```

接着查看startActivityForResult方法，如下所示：

frameworks/base/core/java/android/app/Activity.java

```java
@Override
public void startActivityForResult(String who, Intent intent, int requestCode, @Nullable Bundle options) {
    Uri referrer = onProvideReferrer();
    if(referrer != null) {
        intent.putExtra(Intent.EXTRA_REFERRER,referrer);
    }
    options = transferSpringboardActivityOptions(options);
    Instrumentation.ActivityResult ar = mInstrumentation.execStartActivity(this,mMainThread,getApplicationThread(),mToken,who,intent,requestCode,options);//1
    if (ar != null) {
        mMainThread.sendActivityResult(mToken,who,requestCode,ar.getResultCode(),ar.getResultData());
    }
    cancelInputAndStartExitTransition(options);
}
```

在注释1处调用了mInstrumentation的execStartActivity方法来启动Activity，剩余的调用代码已经在4.1节介绍了，这里不在赘述。这个**mInstrumentation是Activity的成员变量**，**我们就选择Instrumentation为Hook点**，**用代理Instrumentation来替代原始的Instrumentation来完成Hook**。首先我们先写出代理Instrumentation类，如下所示：

InstrumentationProxy.java

```java
public class InstrumentationProxy extends Instrumentation {
    private static final String TAG = "InstrumentationProxy";
    Instrumentation mInstrumentation;
    public InstrumentationProxy(Instrumentation instrumentation) {
        mInstrumentation = instrumenation;
    }
    public ActivityResult execStartACtivity(Context who, IBinder contextThread, IBinder token, Activity target, Intent intent, int requestCode, Bundle options) {
        Log.d(TAG, "Hook成功" + "---who:"+who);
        try {
            // 通过反射找到Instrumentation的execStartActivity方法
            Method execStartActivity = Instrumentation.class.getDeclaredMethod("execStartActivity",Context.class,IBinder.class,Activity.class,Intent.class,Bundle.class);
            return (ActivityResult) execStartActivity.invoke(mInstrumentation,who,contextThread,token,target,intent,requestCode,options);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

InstrumentationProvy继承了Instrumentation，并包含Instrumentation的引用。InstrumentationProxy实现了execStartActivity方法，其内部会通过反射找到并调用Instrumentation的execStartActivity方法。接下来我们用InstrumentationProxy来替换Instrumentation，代码如下所示：

MainActivity.java

```java
public void replaceActivityInstrumentation(Activity activity) {
    try {
        //得到Activity的mInstrumentation字段
        Field field = Activity.class.getDeclaredField("mInstrumentation");//1
        //取消Java的权限控制检查
        field.setAccessible(true);//2
        Instrumentation instrumentation = (Instrumentation) field.get(activity);//3
        Instrumentation instrumentationProxy = new InstrumentationProxy(instrumentation);//4
        field.set(activity,instrumentationProxy);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

在注释1处得到Activity的成员变量mInstrumentation，这个成员变量是私有的，因此在注释2处取消Java的权限控制检查，这样就可以访问mInstrumentation字段。在注释3处得到Activity中的Instrumentation对象，在注释4处创建InstrumentationProxy并传入注释3处得到的Instrumentation对象，最后用InstrumentationProxy来替换Instrumentation，这样就实现了代理Instrumentation替换Instrumentation的目的。最后在MainActivity的onCreate方法中使用replaceActivityInstrumentation方法，如下所示：

MainActivity.java

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_mian);
        replaceActivityInstrumentation(this);
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setData(Uri.parse("http://liuwangshu.cn"));
        startActivity(intent);
    }
    ···
}
```

在onCreate方法中访问了liuwangshu独立博客，打印的Log如下：

```
D/InstrumentationProxy: Hook成功---who:com.example.liuwangshu.hookinstrumentation.MainActivity@47fcd35
```



#### 14.4.2 Hook Context的startActivity方法

在第5章讲过，Context的实现类为ContextImpl，ContextImpl的startActivity方法如下所示：

frameworks/base/core/java/android/app/ContextImpl.java

```java
@Override
public void startActivity(Intent intent, Bundle options) {
    warnIfCallingFromSystemProcess();
    if((intent.getFlags()&Intent.FLAG_ACTIVITY_NEW_TASK)==0 
       && options != null && ActivityOptions.fromBundle(options).getLaunchTaskId() == -1) {
        throw new AndroidRuntimeException("Calling startActivity() from outside of an Activity "+ "context requires the FLAG_ACTIVITY_NEW_TASK flag."+ "Is this really what you want?");
    }
    mMainThread.getInstrumentation().execStartActivity(getOuterContext(),mMainThread.getApplicationThread(),null,(Activity) null, intent,-1,options);
}
```

最后一行调用了ActivityThread的getInstrumentation方法获取Instrumentation。**ActivityThread是主线程的管理类**，**Instrumentation是ActivityThread的成员变量**，**一个进程中只有一个ActivityThread，因此仍旧选择Instrumentation作为Hook点**，用代理Instrumentation来替换Instrumentation。代理Instrumentation和14.3.1节给出的InstrumentationProxy代码是一样的，接下来我们用InstrumentationProxy来替换Instrumentation，代码如下所示：

MainActivity.java

```java
public void replaceContextInstrumentation() {
    try {
        //获取ActivityThread类
        Class<?> activityThreadClazz = Clazz.forName("android.app.ActivityThread");
        Field activityThreadField = activityThreadClazz.getDeclaredField("sCurrentActivityThread");//1
        activityThreadField.setAccessible(true);
        Object currentActivityThread = activityThreadField.get(null);//2
        //获取ActivityThread中的mInstrumentation字段
        Field mInstrumentationField = activityThreadClazz.getDeclaredField("mInstrumentation");
        mInstrumentationField.setAccessible(true);
        Instrumentation mInstrumentation = (Instrumentation) mInstrumentation;
        Field.get(currentActivityThread);
        Instrumentation mInstrumentationProxy = new InstrumentationProxy(mInstrumentation);//3
        mInstrumentation.set(currentActivityThread, mInstrumentationProxy);
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

首先我们**通过反射获取ActivityThread类**，**ActivityThread类中有一个静态变量`sCurrentActivityThread`**，**用于表示当前的ActivityThread对象**，因此在注释1处获取ActivityThread中定义的sCurrentActivityThread字段，在注释2处获取Field类型的activityThreadField对象的值，这个值就是sCurrentActivityThread对象。同理获取当前ActivityThread的mInstrumentation对象。在注释3处创建InstrumentationProxy并传入此前得到的mInstrumentation对象，最后用InstrumentationProxy来替换mInstrumentation。在MainActivity中使用replaceContextInstrumentation方法，如下所示：

MainActivity.java

```java
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContextView(R.layout.activity_main);
        replaceContextInstrumentation();
        Intent intent = new Intent(Intent.ACTION_VIEW);
        intent.setData(Uri.parse("http://liuwangshu.cn"));
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        getApplicationContext().startActivity(intent);
    }
}
```

在onCreate方法中人就访问了liuwangshu的独立博客，打印的Log为：

```
D/InstrumentationProxy: Hook成功---who:android.app.Applicayion@2f21e30
```



#### 14.4.3 Hook startActivity总结

Hook Context的startActivity方法和Hook Activity的startActivity方法最大的区别就是替换的Instrumentation不同，前者是ActivityThread中的Instrumentation，后者是Activity中的Instrumentation。另外有一点需要注意的是，这里举的例子都是MainActivity的onCreate方法中进行Instrumentation替换的，这里未必是最佳的替换时间点，只是为了方便举例。可以尝试在Activity的attachBaseContext方法中进行Instrumentation替换，因为这个方法要先于Activity的onCreate方法被调用。讲到这里，我们知道了如何使用代理来Hook startActivity方法，简单说就是找到Hook点，再用代理对象来替换Hook点。



### 14.5 本章小结

本章原本是下一章的内容，但是为了讲解得更清晰，所以单拿出来作为一章。本章简单地介绍了Hook技术概述、Hook技术分类和Hook startActivity方法，这些内容主要是为了下一章讲解插件化做铺垫，因此并没有讲解Hook技术的全部内容，想要更多地了解Hook技术请查阅相关专业书籍。



## 插件化原理

随着应用开发的规模和复杂度越来越高，插件化技术被广泛地应用在各个较大规模的应用开发中。插件化技术和热修复技术都属于动态加载技术，从普及率的角度来看，插件化技术没有热修复的普及率高，主要原因是占大多数的中小型应用很少也没有必要去采用插件化技术。虽然插件化技术普及率现在不算高，但是插件化的原理对于应用开发的技术提升有很大的帮助，可以使你更好地理解系统的源码，并将系统源码和应用开发相结合。插件化是一个很庞大的知识体系，用一章的内容只能介绍部分的插件化原理，本章更多的是起一个抛砖引玉的作用。在本书截稿之前，Android P preview 开始限制调用隐藏API，很快也出现了一些绕过限制的方案，但无论采用什么方案，插件化的基本原理还是需要去了解的。阅读本竟前请先阅读开头列出的关联章节，以达到最好的阅读理解效果。

### 15.1 动态加载技术

在讲插件化原理之前，需要先了解它的前身：动态加载技术。动态加载技术不知应用在Android开发领域，在很多开发领域都应用过，这里我们讨论的只是动态加载技术在Android开发领域的应用。在Android传统开发中，一旦应用的代码被打包成APL并被上传到各个渠道市场，我们就不能修改应用的源码了，只能通过服务器来控制应用中预留的分支代码。但是很多时候我们无法提前预知需求和突然发生的情况，也就不能在应用代码中预留分支代码，这时就需要采用动态加载技术。在应用程序运行时，动态加载一些程序中原本不存在的可执行文件并运行这些文件里的代码逻辑。可执行文件总的来说分为两种，一种是动态链接库so，另一种是dex相关文件（dex以及包含dex的jar/apk文件）。看到这里很多读者可能发现了，在第13章我们讲解热修复原理时也提到了上述的可执行文件的加载，这时因为热修复技术本身就是动态加载技术流派派生出来的。随着应用开发技术和业务的逐步发展，动态加载技术派生出两个技术，分别是热修复技术和插件化技术，如图15-1所示。

<img src="./Hook技术.assets/image-20240118170619933.png" alt="image-20240118170619933" style="zoom:67%;" />

其中热修复技术主要用来修复Bug，插件化技术则主要用于解决应用越来越庞大以及功能模块的解耦，围绕着两个技术出现了很多的热修复框架和插件化框架，在第13章我们了解了很多热修复框架，这一章我们将了解一些插件化框架。需要注意的是，动态加载技术本身并没有被官方认可，而且是一个非常规的技术，在国外这门技术关注度并不高，它的产生更多的是国内的业务需求和产品的驱动。



### 15.2 插件化的产生

在讲解插件化原理之前，我们十分有必要了解插件化是如何产生的。

#### 15.2.1 应用开发的痛点和瓶颈

在Android开发早期很少用到动态加载技术，因为这个时候业务需求和应用开发的复杂度都不是很高，但随着互联网的极速发展，会出现一下几种情况。

**1.业务复杂，模块耦合**

随着业务越来越复杂也越来越多，导致应用程序体积越来越大，应用程序的工程和功能模块数量越来越多，一个应用可能是由几十、几百人协同开发的，很多工程和功能模块都是由一个小组进行开发维护的，如果功能模块间的耦合度比较高，修改一个功能模块会影响其他功能模块，势必会极大地增加沟通成本。

**2.应用间的接入**

一个应用不再是单独的应用，它可能需要接入其他的应用。拿手机淘宝来说，它的流量非常大，其他他的淘宝应用或者业务比如：聚划算、淘宝书城、飞猪旅游、淘宝拍卖、淘宝外卖等都希望接入到淘宝客户端，这样既能获取到流量，同时也可以将用户引流到自己的应用中，如果使用常规的技术手段，会产生两个问题。

+ 比如淘宝外卖需要接入到淘宝客户端，那么淘宝外卖团队可能需要维护两个版本，一个自身版本，另一个是淘宝客户端版本，这样维护成本和沟通成本会比较高。况且淘宝外卖不只是接入淘宝客户端，它还可以接入到其他应用中，比如支付宝应用，那么淘宝外卖团队维护的就不仅仅是两个版本了。
+ 比如淘宝客户端接入了很多其他的应用，势必会使应用的体积急剧变大，编译时间会变得非常长，一个Bug和功能就会由组内的开发协作变为了组合组之间甚至部门间的开发协作，极大地增加了开发测试成本和沟通成本，新功能的添加牵扯得越多，版本发布的时间变得越不可控。

**3.65536限制，内存占用大**

在13.4.1节中我们知道了65536限制，随着应用的代码量不断增大，引入的苦也越来越多，特别是应用需要接入其他应用，那么方法书很容易超过65536个。应用代码量的增加同时也导致了应用占用大量的内存。

#### 15.2.2 插件化思想

Android系统本身并没有提供太多的功能，内置的应用数量和整体功能也很有限，它像是一个为人类服务的机器人，只能满足人类基本的需求。初始的机器人只有照相、地图、浏览器、计算机等功能，这显然是有些乏味的，我们可以给这个机器人安装很多其他的应用，使它提供更多的功能。

我们给这个机器人安装了很多应用，这些应用不仅覆盖了人的衣食住行，还提供了娱乐功能，我们可以玩游戏、听音乐和购物等，机器人的功能也得到了极大提升，能够为人类提供更多的服务。这些安装的应用可以理解为插件，这些插件可以自由地进行插拔，比如我们需要玩游戏时可以安装“王者荣耀”，如果不好玩就把它卸载掉。这么说来其实Android、iOS、Mac 等操作系统采用的都是这种思想，也就是插件化思想。

#### 15.2.3 插件化定义

15.2.1节所提出的问题就可以用插件化的思想来解决，如果没有采用插件化，那么手机淘宝客户端的框架可以缩略地理解为如图15-4所示的样子。

<img src="./Hook技术.assets/image-20240118172809974.png" alt="image-20240118172809974" style="zoom:67%;" />

图15-4中采用常规技术的淘宝客户端会分为两大部分，一个是自身的业务模块，比如商城购物、消息和搜索等，另一个是要外接的其他的应用业务，比如聚划算、飞猪履行和淘宝外卖等。如果采用这种常规的技术方案，那么有会产生15.2.1节中提出的各种问题，为了解决这些问题，我们可以采用插件化思想来对淘宝主客户端进行改造，如图15-5所示。

<img src="./Hook技术.assets/image-20240118173130796.png" alt="image-20240118173130796" style="zoom:67%;" />

插件化的客户端由宿主和插件两个部分组成，宿主是指先被安装到手机中的APK，就是平常我们加载的普通APK。插件一般是指经过处理的APK、so和dex等文件，插件可以被宿主进行加载，有的插件也可以作为APK独立运行。可以看出曾云插件化的淘宝客户端，其内部包含了主界面模块；另一部分是插件部分，不仅包括了外接的其他应用业务，比如聚划算和飞猪旅行，同时也包括了淘宝自身的业务模块，比如消息和搜索。需要注意的是，这里的举例更多是为了便于理解，只是淘宝客户端演进过程中的一个非常缩略的框架，和真实的淘宝客户端由非常大的区别。讲到这里就可以引入插件化的定义：将一个应用按照插件的方式进行改造的过程就叫作插件化。采用了插件化的淘宝主客户端就避免了15.2.1节提出的各种问题，在协作方面插件可以由一个人或者一个小组来进行开发，这样各个插件之间，以及插件和宿主之间的耦合度会降低。应用间的接入和维护也变得便捷，每个应用团队只需要负责自己的那一部分就可以了。应用以及主dex的体积也会相应变小，间接地避免了65536限制。第一次加载到内存的只有淘宝主客户端，当使用到其他其他插件时才会加载相应插件到内存，这样就减少了内存的占用。



### 15.3 插件化框架对比