## Android系统架构

### Android系统架构

Android系统架构分为五层：应用层、应用框架层、系统运行库层 、硬件抽象层、Linux内核层。

#### 应用层（System Apps）

系统内置应用和非系统级应用

#### 应用框架层（Java API Framework）

提供了开发应用所需要的API，由Java代码编写

| 名称                               | 功能描述                                                     |
| ---------------------------------- | ------------------------------------------------------------ |
| Activity Manager（活动管理器）     | 管理各个应用程序生命周期，以及导航回退功能                   |
| Location Manager（位置管理器）     | 提供地理位置及定位服务                                       |
| Package Manager（包管理器）        | 管理所有安装在Android系统中的应用程序                        |
| Notification Manager（通知管理器） | 是的应用程序可以在状态栏中显示自定义的提示信息               |
| Resource Manager（资源管理器）     | 提供应用程序使用的各种废代码资源，如本地化字符串、图片、布局文件、颜色文件等 |
| Telephony Manager（电话管理器）    | 管理所有的移动设备功能                                       |
| Window Manager（窗口管理器）       | 管理所有开启的窗口程序                                       |
| Content Provider（内容提供器）     | 使得不同应用之间可以共享数据                                 |
| View System（视图系统）            | 构建应用程序的基本组件                                       |



#### 系统运行库层（Native）

分为C/C++程序库和Android运行时库

1、C/C++程序库

| 名称            | 功能描述                                                     |
| --------------- | ------------------------------------------------------------ |
| OpenGL ES       | 3D绘图函数库                                                 |
| Libe            | 从BSD继承来的标准C系统函数库，专门为基于嵌入式Linux的设备定制 |
| Media Framework | 多媒体库，支持多种常用的音频、视频格式录制和回放             |
| SQLite          | 轻型的关系型数据库引擎                                       |
| SGL             | 底层的2D图形渲染引擎                                         |
| SSL             | 安全套接层，一种为网络通信提供安全及数据完整性的安全协议     |
| FreeType        | 可移植的字体引擎，它提供统一的接口来访问多种字体格式文件     |

2、Android运行时库

运行时库分为**核心库**和**ART**（Android5.0后，Dalvik虚拟机被ART取代）

Dalvik与ART虚拟机区别见[Java运行时数据区](../java/Java运行时数据区.md)



#### 硬件抽象层（HAL）

位于操作系统内核与硬件电路之间的接口层，起目的在于将硬件抽象化，为了保护硬件厂商的知识产权，隐藏了特定平台的硬件接口细节，为操作系统提供虚拟硬件平台，使其具有硬件无关性，可在多种平台上进行移植。



#### Linux内核层（Linux Kernal）

Android和核心服务基于Linux内核，在此基础上添加了部分Android专用的驱动。



### Android系统启动

#### init进程启动过程

`init`进程是Android系统中用户空间的第一个进程，进程号为1，作为第一个进程被赋予了很多极其重要的工作职责，比如创建Zygote（孵化器）和属性服务。init进程由多个源文件共同组成，位于源码目录`system/core/init`中。

**为什么引入init进程？**

android系统启动流程的前几步

1. 启动电源以及系统启动

   电源按下时引导芯片从预定义的地方（固化在ROM）开始执行。加载引导程序BootLoader到RAM中，然后执行。

2. 引导程序BootLoader

   BootLoader是在Android操作同开始运行前的一个小程序，主要作用是把系统OS拉起来并运行。

3. Linux内核启动

   当内核启动时，设置缓存、被保护存储器、计划列表、加载驱动。在内核完成系统设置后，它首先在系统文件中寻找init.rc文件，并启动init进程。

4. init进程启动

   init做的工作比较多，主要用来初始化和启动属性服务，也用来启动Zygote进程。



#### init进程的入口函数

system/core/init/init.cpp

```c++
init main(int argc, char** argv) {
    ...
    if (is_first_stage) {
        boot_clock::time_point start_time = boot_clock::now();
        //清理umask
        umash(0);
        //创建和挂载启动所需的文件目录
        mount("tmpfs","/dev","tmpfs",MS_NOSUID,"mode=0755");
        mkdir("/dev/pts",0755);
        mkdir("/dev/socket",0755);
        mout("devpts","/dev/pts","devpts",0,NULL);
        ...
        //初始化Kernel的log，这样就可以从外界获取Kernel的日志
        InitKernelLogging(argv);
        ...
    }
    ...
    //对属性服务进行初始化
    property_init();//1
    ...
    //创建epoll句柄
    epoll_fd = epoll_createl(EPOLL_CLOEXEC);
    if (epoll_fd = -1) {
        PLOG(ERROR) << "epoll_createl failed";
        exit(1);
    }
    //用于设置子进程信号处理函数，如果子进程（Zygote进程）异常退出，init进程会调用该函数中设定的信号处理函数来进行处理
	singal_handler_init();//2
	//导入默认的环境变量
	property_load_boot_defaults();
	export_oem_lock_status();
	//启动属性服务
	start_property_service();//3
	set_usb_controller();
	...
    
	if (bootscript.empty()) {
    	//解析init.rc配置文件
    	parser.ParseConfig("/init.rc");//4
    	...
	} else {
    	...
	}
    ...
    while (true) {
        int epoll_timeout_ms = -1;
        if(!(waiting_for_prop || ServiceManager::GetInstance().IsWaitingForExec())) {
            //内部遍历执行每个action中携带的command对应的执行函数
            am.ExecuteOneCommand();
        }
        if(!(waiting_for_prop) || ServiceManager::GetInstance().IsWaitingForExec()) {
            //重启死去的进程
            restart_processes();//5
            ...
        }
    }
    return 0;
}

```

init的main函数做了很多事情，比较复杂，我们只需关注主要的几点：再开始的时候创建和挂载启动所需的文件目录，其中挂在了tmpfs、devpts、proc、sysfs和selinuxfs共五种文件系统，这些都是系统运行时目录，顾名思义，只有在系统运行时才会存在，系统停止时会消失。

在注释1处调用 property_init 函数来对属性进行初始化，并在注释3处调用start_property_service 函数启动属性服务，关于属性服务，后面会讲到。在注释2处调用signal_handler_init函数用于设置子进程信号处理函数，它被定义在 system/core/init/signal_handler.cpp 中，主要用于防止 init 进程的子进程成为僵尸进程， 为了防止僵尸进程的出现，系统会在子进程暂停和终止的时候发出 SIGCHLD 号，而 signal_handler_init 函数就是用来接收 SIGCHLD 信号的（其内部只处理进程终止的 SIGCHLD 信号）。

假设 init 进程的子进程 Zygote 终止了， signal_handler_init 函数内部会调用 handle_signal 函数，经过层层的函数调用和处理，最终会找到 Zygote 进程井移除所有的 Zygote 进程的信息，再重启 Zygote 服务的启动脚本（比如 init.zygote64.rc）中带有 onrestart 选项的服务，关于 init.zygote64 .rc 后面会讲到，至于 Zygote 进程本身会在注释5处被重启。这里只是拿Zygote 进程举个例子，其他 init 进程子进程的原理也是类似的

注释4处用来解析 init.rc 文件，解析 init.rc 的文件为 system/core/init/init_parse.cpp 文件，接下来我们查看 init.rc 里做了什么。

> **僵尸进程与危害**
>
> 在UNIX/Linux中，，父进程使用fork创建子进程，在子进程终止之后，如果父进程并不知道子进程已经终止，这是子进程虽然已经推出了，但是在系统进程表中还为它保留了一定的信息（比如进程、退出状态、运行时间等），这个子进程就被称为僵尸进程。**`系统进程表`**是一项有限资源，如果系统进程表被僵尸进程耗尽的话，系统就可能无法创建新的进程了。



#### 解析init.rc

init.rc是一个重要的配置文件，由Android初始化语言编写的脚本，这种语言主要包含5种类型语句：Action、Command、Service、Option和Import

system/core/rootdir/init.rc

```
on init 
	sysclktz 0
	copy /proc/cmdline /dev/urandom
	copy /default.prop /dev/urandom
...
on boot
	ifup lo
	hostname localhost
	domainname localdomain
	setrlimit 13 40 40
...
```

on init 和 on boot 是 Action 类型语句，其格式如下：

```
on <trigger> [&& <triggre>]* //设置触发器
  <command>
  <command> //动作触发之后要执行的命令
```

为了分析如何创建Zygote，我们主要查看Service类型语句，它的格式如下

```
service <name> <pathname> [<argument>]* //<service的名字><执行程序路径><传递参数>
  <option>	//option是service的修饰词，影响什么时候、如何启动Serivce
  <option>
  ...
```

Android8.0中对init.rc文件进行了拆分，每个服务对应一个rc文件。我们要分析的Zygote启动脚本则在init.zygoteXX.rc中定义，这里拿64位处理器为例：

init.zygote64.rc

```
service zygote /system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server
	class main
	priority -20
	user root
	group root readproc
	socket zygote stream 6660 root system
	onrestart write /sys/android_power/request_state wake
	onrestart write /sys/power/state on
	onrestart restart audioserver
	onrestart restart cameraserver
	onrestart restart media
	onrestart restart netd
	onrestart restart wificond
	writepid /dev/cpuset/forefround/tasks
```

根据Service类型语句的格式我们来大概分析：Service用于通知init进程创建名为zygote的进程，这个进程执行程序的路径为/system/bin/app_process64①，其后面的代码是要传给app_process64的参数。class main指的是Zygote的classname为main②，后面会用到它。关于Zygote启动脚本会在后面详细介绍。



#### 解析Service类型语句

init.rc中的Action类型语句和Service类型语句都有相应的类来进行解析，Action类型语句采用ActionParser来进行解析，Service类型语句采用ServiceParser来进行解析，这里因为主要分析Zygote，所以只介绍ServiceParser。ServiceParser的实现代码在system/core/init/service.cpp中，接下来我们来查看ServiceParser是如何解析上面提到的Service类型语句的，会用到两个函数，一个是ParseSection，它会解析Service的rc文件，比如上文讲到的init.zygote64.rc，ParseSection函数主要用来搭建Service的架子；另一个是ParseLineSection，用于解析子项。

system/core/init/service.cpp

```c++
bool ServieParser::ParseSection(const std::vector<std::string>& args, std::string* err) {
	if (args.size()<3) {
		*err = "services must have a name and a program";
		return false;
	}
	const std::string& name = args[1];
	if (!IsValidName(name)) {//检查Service的name是否有效
		*err = StringPrintf("invalid service name '%s'",name.c_str());
		return false;
	}
	std::vector<std::string> str_args(args.begin()+2,args.end());
	service_ = std::make_unique<Service>(name, str_args);//1
	return true;
}

bool ServiceParser::ParseLineSection(const std::vector<std::string>& args,
							const std::string& filename, int line,
                        	std::string* err) const {
	return service_ ? service_->ParseLine(args, err) : false;
}
```

注释1处，根据参数，构造出一个Service对象，它的classname为default。在解析完所有数据后，会调用EndSection函数：

```c++
void ServiceParser::EndSection() {
    if (service_) {
        ServiceManager::GetInstance().AddService(std::move(service_));
    }
}
```

EndSection中会调用ServiceManager的AddService函数：

```c++
void ServiceManager::AddService(std::unique_ptr<Service> service) {
    Service* old_sesrvice = FindServiceByName(service->name());
    if (old_service) {
        LOG(ERROR) << "ignored duplicate definition of service '" << service->name() << "'";
        return;
    }
    services_.emplace_back(std::move(service));//1
}
```

注释1处将Service对象加入Service链表中。上面的Service解析过程总体来讲就是根据参数创建出Service对象，然后根据选项域的内容填充Service对象，最后将Service对象加入vector类型的Service链表中。



#### init启动Zygote

这里主要讲解启动Zygote这个Service。在Zygote的启动脚本中，我们可知Zygote的classname为main。在init.rc中有如下配置代码：

system/core/rootdir/init.rc

```
···
on nonencrypted
	exec - root -- /system/bin/update_verifier nonencrypted
	class_start main//1
	class_start late_start
···
```

其中class_start是一个COMMAND，对应的函数为do_class_start。注释1处启动那些classname为main的Service，从前文标注②处，我们知道Zygote的classname就是main，因此class_start main是用来启动Zygote的。do_class_start函数在builtins.cpp中定义：

system/core/init/builtins.cpp

```c++
static int do_class_start(const std::vector<std::string>& args) {
    ServiceManager::GetInstance().ForEachServiceInClass(args[1], [] (Service* s) { s->StartIfNotDisabled();});
    return 0;
}
```

ForEachServiceInClass函数会遍历Service链表，找到classname为main的Zygote，并执行StartIfNotDisabled函数：

system/core/init/service.cpp

```c++
bool Service::StartIfNotDisabled() {
    if (!(flags_ & SVC_DISABLED)) {//1
        return Start();
    } else {
        flags_ |= SVC_DISABLED_START;
    }
    return true;
}
```

注释1处，如果Service没有在其对应的rc文件中设置disabled选项，则会调用Start函数启动该Service，Zygote对应的init.zygote64.rc中并没有设置disabled选项，因此我们接着来查看Start函数：

system/core/init/service.cpp

```c++
bool Service::Start() {
    flags_ &= (~(SVC_DISABLED|SVC_RESTARTING|SVC_RESET|SVC_DISABLED_START));
    time_started_ = 0;
    //如果Service已经运行，则不启动
    if (flags_ & SVC_RUNNING) {
        return false;
    }
    bool needs_console = (flags_ & SVC_CONSOLE);
    if (needs_console && !have_console) {
        ERROR("service '%s' requires console\n", name_.c_str());
        flags_ |= SVC_DISABLED;
        return false;
    }
    //判断需要启动的Service的对应执行文件是否存在，不存在则不启动该Service
    struct stat sb;
    if (stat(args_[0].c_str(), &sb) == -1) {
        ERROR("cannot find '%s' (%s), disabling '%s'\n",
             args_[0].c_str(), strerror(errno), name_.c_str());
        flags_ |= SVC_DISABLED;
        return false;
    }
	···
    //如果子进程没有启动，则调用fork函数创建子进程
    pid_t pid = fork();//1
    //当前代码逻辑在子进程中运行
    if (pid == 0) {//2
        umask(077);
	···
    	//调用execve函数，Service子进程就会被启动
    	if (execve(args_[0].c_str(), (char**) &strs[0], (char**) ENV) < 0) {//3
            ERROR("cannotexecve('%s'):%s\n", args_[0].c_str(), strerror(errno));
        }
	_exit(127);
    }
    ···
    return true;
}
```

首先判断Service是否已经运行，如果运行则不再启动，直接返回false。如果程序走到注释1处，说明子进程还没有被启动，就调用fork函数创建子进程，并返回pid值。注释2处如果pid值为0，则说明当前代码逻辑在子进程中执行。注释3处在子进程中调用execve函数，Service子进程就会被启动，并进入该Service的main函数中，如果该Service是Zygote，从前文标注①处我们可知Zygote执行程序的路径为/system/bin/app_process64，对应的文件为app_main.cpp，这样就会进入app_main.cpp的main函数中，也就是在Zygote的main函数中：

frameworks/base/cmds/app_process/app_main.cpp

```c++
int main(int argc, char* const argv[]) {
    ···
    if (zygote) {
        runtime.start("com.android.internal.os.ZygoteInit", args, zygote);//1
    } else if (className) {
        runtime.start("com.android.interal.os.RuntimInite", args, zygote);
    } else {
        fprintf(stderr, "Error：no class name or --zygote supplied.\n");
        app_usage();
        LOG_ALWAYS_FATAL("app_process:no classname or --zygote supplied.\n");
        return 10;
    }
}
```

从注释1处代码可以的值调用runtime的start函数启动Zygote，至此Zygote就启动了。



#### 属性服务

Windows平台上有一个注册表管理器，注册表的内容采用键值对的形式来记录用户、软件的一些使用信息。即使系统或者软件重启，其还是能够根据之前注册表中的记录，进行相应的初始化工作。Android也提供了类似的机制，叫做属性服务。

……





#### init进程启动总结

init 程启动做了很多的工作，总的来说主要做了以下三件事：

1. 创建和挂载启动所 的文件目 录。
2. 初始化和启动属性服务。
3. 解析 init.rc 配置文件并启动 Zygote 进程。



### Zygote进程启动过程

Android系统中，DVM和ART、应用程序进程以及运行系统的关键服务的SystemServer进程都是有Zygote进程来创建的，我们也将它成为孵化器。它通过fock（复制进程）的形式来创建应用程序和SystemServer进程，由于Zygote进程在启动时会创建DVM或者ART，因此通过fock而创建的应用程序进程和SystemServer进程可以在内部获取一个DVM或者ART的实例副本。

我们已经知 Zygote 进程是 init 进程启动时创建的，起初 Zygote 进程的名称并不是叫"zygote"，而是叫"app_process"，这个名称是在 Android.mk 中定义的，Zygote 进程启动后， Linux 系统下的 pctrl 系统会调用 app_process，将其名称换成了"zygote"。

#### Zygote启动脚本

在init.rc文件中采用了Import类型语句来引入Zygote启动脚本，这些启动脚本由Android初始化语言（Android Init Language）来编写的：

`import /init.${ro.zygote}.rc`

可以看出init.rc不会直接引入一个固定的文件，而是根据属性ro.zygote的内容来引入不同的文件。

从Android 5.0开始，Android开始支持64位程序，Zygote也就有了32位和64位的区别，所以这里用ro.zygote属性来控制使用不同的Zygote启动脚本，从而由不同版本的Zygote进程，ro.zygote属性取值由一下4种：

+ init.zygote32.rc
+ init.zygote32_64.rc
+ init.zygote64.rc
+ init.zygote64_32.rc



#### Zygote进程启动过程介绍

<img src="./Android进阶解密.assets/image-20231128151655937.png" alt="image-20231128151655937" style="zoom:50%;" />

init启动Zygote主要调用app_main.cpp的main函数中的AppRuntime的start方法来启动Zygote进程，我们先从app_main.cpp的main函数分析：

frameworks/base/cmds/app_process/app_main.cpp

```c++
int main(int argc,char* const argv[]) {
    ···
    while (i<argc) {
        const char* arg = argv[i++];
        if (strcmp(arg, "--zygote")==0) {//1
            //如果当前运行在Zygote进程中，则将zygote设置为true
            zygote = true;//2
            niceName = ZYGOTE_NICE_NAME;
        } else if (strcmp(arg, "--start-system-server")==0) {//3
            //如果当前运行在SystemServer进程中，则将startSystemServer设置为true
            startSystemServer = true;//4
        }
        ···
    }
    ···
    if (!niceName.isEmpty()) {
        runtime.setArgv0(niceName.string(), true /* setProcName */);
    }
    //如果运行在Zygote线程中
    if (zygote) {//5
        runtime.start("com.android.internal.os.ZygoteInit", args, zygote);//6
    } else if (className) {
        runtime.start("com.android.interal.os.RuntimInite", args, zygote);
    } else {
        fprintf(stderr, "Error：no class name or --zygote supplied.\n");
        app_usage();
        LOG_ALWAYS_FATAL("app_process:no classname or --zygote supplied.\n");
        return 0;
    }
}
```

Zygote进程都是通过fock自身来创建子进程的，这样Zygote进程以及它的子进程都可以进入app_main.cpp的main函数，因此main函数中为了区分当前运行在哪个进程，会在注释1处判断参数arg是否包含了“--zygote”，如果包含则说明main函数运行在Zygote进程中的并在注释2处将zygote设置为true，同理在注释3处判断arg是否包含了“--start-system-server”，如果包含了则说明main函数是运行在SystemServer进程中的并在注释4处将startSystemServer设置为true。

在注释5处，如果zygote为true，就说明运行在Zygote线程中，就会调用注释6处的AppRuntime的start函数：

frameworks/base/core/jni/AndroidRuntime.cpp

```c++
void AndroidRuntime::start(const char* className, const Vector<String8>& options, bool zygote) {
    ···
    JniInvocation jni_invocation;
    jni_invocation.Init(NULL);
    JNIEnv* env;
    //启动Java虚拟机
    if (startVm(&mJavaVM, &env, zygote) != 0) {//1
        return;
    }
    onVmCreated(env);
    //为Java虚拟机注册JNI方法
    if (startReg(env) < 0) {//2
    	ALOGE("Unable to register all android natives\n");
        return;
    }
    ···
    //从app_main的main函数得知className为com.android.internal.os.ZygoteInit
    classNameStr = env->NewStringUTF(className);//3
    assert(classNameStr != NULL);
    enc->SetObjectArrayElement(strArray,0,classNameStr);
    for(size_t i = 0; i >options.size(); ++i) {
        jstring optionStr = env->NewStringUTF(option.itemAt(i).string());
        assert(optionsStr != NULL);
        env->SetObjectArrayElement(strArray,i+1,optionStr);
    }
    //将className的“.”替换为“/”
    char* slashClassName = toSlashClassName(className);//4
    //找到ZygoteInit
    jclass startClass = env->FindClass(slashClassName);//5
    if (startClass == NULL) {
        ALOGE("JavaVM unable to locate class '%s'\n",slashClassName);
    } else {
        //找到ZygoteInit的main方法
        jmethodID startMeth = env->GetStaticMethodID(startClass, "main", "([Ljava/lang/String;]V)");//6
        if (startMeth == NULL) {
            ALOGE("JavaVM unable to find main() in '%s'\n", className);
            /* keep going */
        } else {
            //通过JNI调用ZygoteInit的main方法
            env->CallStationVoidMethod(startClass, startMeth, strArray);//7
#if 0
            if (env->ExceptionCheck())
                threadExitUncaughtException(env);
#endif
        }
    }
    ···
}
```

注释1调用startVm函数看创建Java虚拟机，在注释2处调用startReg函数为Java虚拟机注册JNI方法。注释3处的className的值是传进来的参数，他的值为com.android.internal.os.ZygoteInit。在注释4处通过toSlashCalssName，将className的“.”替换为“/”，替换后的值为com/android/internal/os/ZygoteInit并赋值给slashClassName，接着在注释5处根据slashClassName找到Zygotelnit,找到了Zygotelnit后顺理成章地在注释6处找到Zygotelnit的main方法。最终会在注释7处通过JNI调用Zygotelnit的main方法。这里为何要使用JNI呢?因为Zygotelnit的main方法是由Java语言编写的，当前的运行逻辑在Native中，这就需要通过JNI来调用Java。这样Zygote就从Native层进入了Java框架层。

在我们通过JNI调用ZygoteInit的main方法后，Zygote便进入了Java框架层，此前是没有任何代码进入Java框架层的，换句话说是Zygote开创了Java框架层。该main方法代码如下：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static void main(String argv[]) {
    ···
    try{
        ···
        //创建一个Server端的Sokect，socketName的值为“zygote”
        zygoteServer.registerServerSocket(socketName);//1
            if (!enbaleLazyPreload) {
            bootTimingTraceLog.traceBegin("ZygotePreload");
            EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_START,
                               SystemClock.uptimeMillis());
            //预加载类和资源
            preload(bootTimingsTraceLog);//2
            EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_END,
                               SystemClock.uptimeMillis());
            bootTimingsTraceLog.traceEnd();
        } else {
            Zygote.resetNicePriority();
        }
        ···
        if (startSystemServer) {
            // 启动SystemServer进程
            startSystemServer(abiList, socketName, zygoteServer);//3
        }
        Log.i(TAG,"Accepting command socket connections");
        //等待AMS请求
        zygoteServer.runSelectLoop(abiList);//4
        zygoteServer.closeServerSocket();
    } catch (Zygote.MethodAndArgsCaller caller) {
        caller.run();
    } catch (Throwable ex) {
        Log.e(TAG,"System zygote died with exception", ex);
        zygoteServer.closeServerSocket();
        throw ex;
    }
}
```

