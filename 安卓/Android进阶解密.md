## Android系统架构与系统启动

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



## Android系统启动

### init进程启动过程

`init`进程是Android系统中用户空间的第一个进程，进程号为1，作为第一个进程被赋予了很多极其重要的工作职责，比如创建Zygote（孵化器）和属性服务。init进程由多个源文件共同组成，位于源码目录`system/core/init`中。

**为什么引入init进程？**

android系统启动流程的前几步

1. 启动电源以及系统启动

   电源按下时引导芯片从预定义的地方（固化在ROM）开始执行。加载引导程序BootLoader到RAM中，然后执行。

2. 引导程序BootLoader

   BootLoader是在Android操作系统开始运行前的一个小程序，主要作用是把系统OS拉起来并运行。

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

init.rc是一个重要的配置文件，由Android初始化语言（Android Init Language）编写的脚本，这种语言主要包含5种类型语句：Action、Command、Service、Option和Import

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

1. **创建和挂载启动所需的文件目录**。
2. 初始化和启动**属性服务**。
3. 解析 init.rc 配置文件并**启动 Zygote 进程**。



### Zygote进程启动过程

Android系统中，**DVM和ART**、**应用程序进程**以及**运行系统的关键服务的SystemServer进程**都是有Zygote进程来创建的，我们也将它成为孵化器。它通过fock（复制进程）的形式来创建应用程序和SystemServer进程，由于Zygote进程在启动时会创建DVM或者ART，因此通过fock而创建的应用程序进程和SystemServer进程可以在内部获取一个DVM或者ART的实例副本。

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

前面的流程：

1. `init.rc` （class_start main）——> 启动Zygote服务
2. `builtins.cpp` （do_class_start() ——> ForEachServiceInClass()）——> 遍历Service链表来找并执行StartIfNotDisabled函数
3. `service.cpp` （StartIfNotDisabled() ——> Start() ——> execve()）——> 进入该Service的main函数
4. `app_main.cpp` （runtime.start()）——> 至此Zygote启动

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

##### 1、AndroidRuntime.cpp

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

注释1调用startVm函数来创建Java虚拟机，在注释2处调用startReg函数为Java虚拟机注册JNI方法。注释3处的className的值是传进来的参数，他的值为com.android.internal.os.ZygoteInit。在注释4处通过toSlashClassName，将className的“.”替换为“/”，替换后的值为com/android/internal/os/ZygoteInit并赋值给slashClassName，接着在注释5处根据slashClassName找到Zygotelnit,找到了Zygotelnit后顺理成章地在注释6处找到Zygotelnit的main方法。最终会在注释7处通过JNI调用Zygotelnit的main方法。这里为何要使用JNI呢?因为Zygotelnit的main方法是由Java语言编写的，当前的运行逻辑在Native中，这就需要通过JNI来调用Java。这样Zygote就从Native层进入了Java框架层。

在我们通过JNI调用ZygoteInit的main方法后，Zygote便进入了Java框架层，此前是没有任何代码进入Java框架层的，换句话说是Zygote开创了Java框架层。该main方法代码如下：

##### 2、ZygoteInit.java

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

在注释1处通过 registerServerSocket 方法来创建 Server 端的 Socket ，这个 name 为“zygote”的 Socket 用于等待 ActivityManagerService 请求 Zygote 来创建新的应用程序进程，关于 AMS 将在第6章进行介绍。在注释2处预加载类和资源。在注释3处启动SystemServer 进程，这样系统的服务也会由 SystemServer 进程启动起来。在注释4处调用ZygoteServer runSelectLoop 方法来等待 AMS 请求创建新的应用程序进程。由此得知，Zygotelnit 的 main 方法主要做了4件事：

**（1）创建一个Server端的Socket**

**（2）预加载类和资源**

**（3）启动 SystemServer 进程**

**（4）等待 AMS 请求创建新的应用程序**

对一三四件事进行介绍：

**1、registerZygoteSocket**

首先ZygoteServer的registerZygoteSocket方法：

frameworks/base/core/java/com/android/internal/os/ZygoteServer.java

```java
void registerServerSocket(String socketName) {
    if(mServerSocket == null) {
        int fileDesc;
        // 拼接Socket的名称
        final String fullSocketName = ANDROID_SOCKET_PREFIX + socketName;//1
        try {
            //得到Socket的环境变量的值
            String env = System.getenv(fullSocketName);//2
            //将Socket环境变量的值转换为文件描述符的参数
            fileDesc = Integer.parseInt(env);//3
        } catch (RuntimeException ex) {
            throw new RuntimeException(fullSocketName + "unset or invalid", ex);
        }
        try {
            //创建文件描述符
            FileDescription fd = new FileDescription();//4
            fd.setInt$(fileDesc);//5
            //创建服务器端Socket
            mServerSocket = new LocalServerSocket(fd);//6
        } catch (IOException ex) {
            throw new RuntimeException (
                "Error binding to local socket '" + fileDesc + "'",ex)
        }
    }
}
```

在注释1处拼接Socket的名称，其中ANDROID SOCKET PREFIX的值为“ANDROIDSOCKET_”，socketName 的值是传进来的值，等于“zygote”，因此fullSocketName 的值为ANDROID SOCKET zygote”。在注释2处将fullSocketName 转换为环境变量的值，再在注释 3处转换为文件描述符的参数。在注释 4 处创建文件描述符，并在注释 5 处传入此前转换的文件操作符参数。在注释 6 处创建 LocalServerSocket，也就是服务器端的 Socket,并将文件操作符作为参数传进去。在 Zygote 进程将 SystemServer进程启动后，就会在这个服务器端的Socket 上等待AMS请求Zygote进程来创建新的应用程序进程。

**2、启动SystemServer进程**

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
private static boolean startSystemServer(String abiList, String socketName, ZygoteServer zygoteServer) throws Zygote.MethodAndArgsCaller, RuntimeException {
    ···
    //创建args数组，这个数组用来保存启动SystemServer的启动参数
    /*1*/
    String args[] = {
        "--setuid=1000",
        "--setgid=1000",
        "--setgroups=1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1021,1023,1032,3001,3002,3003,3006,3007,3009,3010",
        "--capabilities=" + capabilities + "," + capabilities, 
        "--nice-name=system_server",
        "--runtime-args",
        "--runtime-args",
        "com.android.server.SystemServer",
    };
    ZygoteConnection.Arguments parseArgs = null;
    int pid;
    try {
        parseArgs = new ZygoteConnection.Arguments(args);//2
        ZygoteConnection.applyDebuggerSystemProperty(parsedArgs);
        ZygoteConnection.applyInvokeWithSystemProperty(parseArgs);
        /**
        * 3创建一个子进程，也就是SystemServer进程
        */
        pid = Zygote.forkSystemServer(
        		parsedArgs.uid, parsedArgs.gid,
        		parsedArgs.gids,
        		parsedArgs.debugFlags,
        		null,
        		parsedArgs.permittedCapabilities,
        		parsedArgs.effectiveCapabilities);
    } catch (IllegalArgumentException ex) {
        throw new RuntimException(ex);
    }
    //当前代码逻辑运行在子进程中
    if (pid == 0) {
        if (hasSecondZygote(abiList)) {
            waitForSecondaryZygote(socketName);
        }
        zygoteServer.closeServerSocket();
        //处理SystemServer进程
        handleSystemServerProcess(parseArgs);//4
    }
    return true;
}
```

注释1处的代码用来创建args数组，这个数组用来保存启动SystemServer的启动参数，其中可以看出SystemServer进程的用户id和用户组被设置为1000，并且拥有用户组1001~1010、1018、1021、1032、3001~3010的权限；进程名为 system server；启动的类名为comandroid.serverSystemServer。在注释2处将args 数组封装成Arguments 对象并供注释3处的 forkSystemServer 函数调用。在注释3处调用 Zygote 的 forkSystemServer 方法，其内部会调用 nativeForkSystemServer 这个 Native 方法，nativeForkSystemServer 方法最终会通过 fork 函数在当前进程创建一个子进程，也就是 SystemServer 进程，如果forkSystemServer 方法返回的 pid 的值为0，就表示当前的代码运行在新创建的子进程中，则执行注释4处的 handleSystemServerProcess 来处理 SystemServer 进程，关于 SystemServer 进程启动会在下文介绍。

**3、runSelectLoop**

启动SystemServer进程后，会执行ZygoteServer的runSelectLoop方法：

frameworks/base/core/java/com/andorid/internal/os/ZygoteServer.java

```java
void runSelectLoop(String abiList) throws Zygote.MethodAndArgsCaller {
    ArrayList<FileDesciptor> fds = new ArrayList<FileDesciptor>();
    ArrayList<ZygoteConnection> peers = new ArrayList<ZygoteConnection>();
    fds.add(mServerSocket.getFileDesciptor());//1
    peers.add(null);
    //无线循环等待AMS请求
    while (true) {
        StructPollfd[] pollFds = new StructPollfd[fds.size()];
        for (int i=0; i < pollFds.length; ++i) {//2
            pollFds[i] = new StructPollfd();
            pollFds[i].fd = fds.get[i];
            pollFds[i].events = (short) POLLIN;
        }
        try {
            Os.poll(pollFds,-1);
        } catch (ErrnoException ex) {
            throw new RuntimeException("poll failed", ex);
        }
        for (int i = pollFds.length-1; i>=0;--i) {//3
        	if ((pollFds[i].revents & POOLIN) == 0) {
                continue;
            }
            if (i==0) {
                ZygoteConnection newPeer = acceptCommandPeer(abiList);//4
                peers.add(newPeer);
                fds.add(newPeer.getFileDesciptor());
            } else {
                boolean done = peers.get(i).runOnce(this);//5
                if (done) {
                    peers.remove(i);
                    fds.remove(i);
                }
            }
        }
    }
}
```

注释1处的 mServerSocket 就是我们在 registerZygoteSocket 函数中创建的服务器端Socket，调用 mServerSocket.getFileDescriptor()函数用来获得该 Socket 的 fd 字段的值并添加到 fd 列表 fds 中。接下来无限循环用来等待 AMS  请求 Zygote 进程创建新的应用程序进程。在注释2处通过遍历将 fds 存储的信息转移到 pollFds 数组中。在注释 3 处对 pollFds 进行遍历，如果 i==0，说明服务器端 Socket 与客户端连接上了，换句话说就是，当前 Zygote进程与AMS 建立了连接。在注释4处通过 acceptCommandPeer 方法得到 ZygoteConnection 类并添加到 Socket 连接列表 peers 中，接着将该 ZygoteConnection 的 fd 添加到 fd 列表 fds中，以便可以接收到AMS 发送过来的请求。如果i的值不等于 0，则说明AMS 向 Zygote进程发送了一个创建应用进程的请求，则在注释 5 处调用 ZygoteConnection 的 runOnce函数来创建一个新的应用程序进程，并在成功创建后将这个连接从 Socket 连接列表 peers 和 fd 列表 fds 中清除。

#### Zygote进程启动总结

1. 创建AppRuntime并调用其start方法，启动Zygote进程
2. 创建Java虚拟机并为Java虚拟机注册JNI方法（AndroidRuntime的start方法中）
3. 通过JNI调用ZygoteInit的main函数进入Zygote的Java框架层
4. 通过registerZygoteSocket方法创建服务器端Socket，并通过runSelectLoop方法等待AMS的请求来创建新的应用程序进程
5. 启动SystemServer进程



### SystemServer处理过程

SystemServer进程主要用于创建**系统服务**，**AMS**、**WMS**和**PMS**都由它来创建。

#### Zygote处理SystemServer进程

<img src="./Android进阶解密.assets/image-20231204092629923.png" alt="image-20231204092629923" style="zoom: 50%;" />

在ZygoteInit.java的startSystemServer方法中启动了SystemServer进程：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
privare static boolean startSystemServer(String abiList, String socketName) throws MethodAndArgsCaller, RuntimeException {
    ···
    // 当前运行在SystemServer进程中
    if (pid == 0) {
        if (hasSecondZygote(abiList)) {
            waitForSecondaryZygote(socketName);
        }
        // 关闭Zygote进程创建的Socket
        zygoteServer.closeServerSocket();//1
        handleSystemServerProcess(parsedArgs);//2
    }
    return true;
}
```

SytemServer进程复制了Zygote进程的地址空间，因此也会得到Zygote进程创建的Socket，这个Socket对于SystemServer进程没有用处，因此需要注释1处的代码来关闭该Socket，接着在注释2处调用handleSystemServerProcess方法来启动SystemServer进程。handleSystemServerProcess方法如下：

frameworks/baes/core/java/com/android/internal/os/ZygoteInit.java

```java
private static void handleSystemServerProcess (ZygoteConnection.Arguments parseArgs) {
    ···
    if (parseArgs.invokeWith != null) {
        ···
    } else {
        ClassLoader cl = null;
        if (systemServerClasspath != null) {
            cl = createPathClassLoader(systemServerClasspath, parsedArgs.targetSdkVersion);//1
            Thread.currentThread().setContextClassLoader(cl);
        }
        ZygoteInit.zygoteInit(parsedArgs.targetSdkVersion, parseArgs.remainingArgs, cl);//2
    }
}
```

注释1处创建了PathClassLoader，关于PathClassLoader在十二章介绍，在注释2处调用了ZygoteInit的zygoteInit方法：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static final void zygoteInit(int targetSdkVersion, String[] argv, ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    if (RuntimeInit.DEBUG) {
        Slog.d(RuntimeInit.TAG, "RuntimeInit: Starting application from zygote");
    }
    Trace.tracingBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER,"ZygoteInit");
    RuntimeInit.redirectLogStreams();
    RuntimeInit.commonInit();
    // 启动Binder线程池
    ZygoteInit.nativeZygoteInit();//1
    // 进入SystemServer的main方法
    RuntimInit.appilicationInit(targetSdkVersion, argv,classLoader);//2
}
```

在注释1处调用nativeZygoteInit方法，一看方法的名称调用的是Native层的代码，用来启动Binder线程池，这样SystemServer进程就可以使用Binder与其他进程进行通信了。注释2处是用于SystemServer的main方法，现在分别对注释1和注释2的内容进行介绍。

##### 1、启动Binder线程池

nativeZygoteInit是一个Native方法，因此我们了解了它对应的JNI文件。

frameworks/base/core/jni/AndroidRuntime.cpp

```java
int register_com_android_internal_os_ZygoteInit(JNIEnv* env) {
    const JNINaticeMethod methods[] = {
        {"nativeZygoteInit", "()V",
         (void*) com_android_internal_os_ZygoteInit_nativeZygoteInit},
    }
}
```

通过JNI的gMethods数组，可以看出nativeZygoteInit方法对应的是JNI文件AndroidRuntime.cpp的com_android_internal_os_ZygoteInit_nativeZygoteInit函数：

frameworks/base/core/jni/AndroidRuntime.cpp

```java
static void com_android_internal_os_ZygoteInit_nativeZygoteInit(JNIEnv* env, jobject clazz) {
    gCurRuntime->onZygoteInit();
}
```

这里gCurRuntime是AndroidRuntime类型的指针，具体指向的是AndroidRuntime的子类AppRuntime，它在app_main.cpp中定义，我们接着来查看AppRuntime的onZygoteInit方法：

frameworks/base/cmds/app_process/app_main.cpp

```java
virtual void onZygoteInit() {
    sp<ProcessState> proc = ProcessState::self();
    ALOGV("App process: starting thread pool.\n");
    proc->startThreadPool();//1
}
```

注释1处的代码用来启动一个Binder线程池，这样SystemServer进程就可以使用Binder与其他进程进行通信，看到这里我们知道RuntimeInit.java的nativeZygoteInit函数主要是用来启动Binder线程池。

##### 2、进入SystemServer的main方法

再回到RuntimeInit.java代码，在注释2处调用了RuntimeInit的applicationInit方法：

frameworks/base/core/java/com/android/internal/os/RuntimeInit.java

```java
protected static void applicationInit(int targetSdkVersion, String[] argv, ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    ···
    invokeStaticMain(args.startClass, args.startArgs, classLoader);
}
```

在applicationInit方法中主要调用了invokeStaticMain方法：

frameworks/base/core/java/com/andorid/internal/os/RuntimeInit.java

```java
private static void invokeStaticMain(String className, String[] argv, ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    Class<?> cl;
    try {
        //通过反射得到SystemServer类
        cl = Class.forName(className, true, classLoader);//1
    } catch (ClassNotFoundException ex) {
        throw new RuntimeException("Missing class when invoking static main "+ className);
    }
    Method m;
    try {
        //找到SystemServer的main方法
        m = cl.getMethod("main", new Class[] {String[].class});//2
    } catch (NoSuchMethodException ex) {
        throw new RuntimeException("Missing static main on " + className, ex);
    } catch (SecurityException ex) {
        throw new RuntimeException("Problem getting static main on " + className, ex);
    }
    int modifiers = m.getModifiers();
    if (!(Modifier.isStatic(modifiers) && Modifier.isPublic(modifiers))) {
        throw new RuntimeException("Main method is not public and static on " + className);
    }
    throw new Zygote.MethodAndArgsCaller(m, argv);//3
}
```

注释1处的className为com.android.server.SystemServer，通过反射返回的cl为SystemServer类。在注释2处找到SystemServer中的main方法。在注释3处将找到的main方法传入MethodAndArgsCaller异常中并抛出该异常，捕获MethodAndArgsCaller异常的代码在Zygotelnitjava的main方法中，这个main方法会调用SystemServer的main方法。那么为什么不直接在invokeStaticMain方法中调用SystemServer的main方法呢?原因是这种抛出异常的处理会清除所有的设置过程需要的堆栈帧，并让SystemServer的main方法看起来像是SystemServer进程的入口方法。在Zygote启动了SystemServer进程后，SystemServer进程已经做了很多的准备工作，而这些工作都是在SystemServer的main方法调用之前做的，这使得SystemServer的main方法看起来不像是SystemServer进程的入口方法，而这种抛出异常交由Zygotelnit.java的main方法来处理，会让SystemServer的main方法看起来像是SystemServer进程的入口方法。

下面来查看在ZygoteInit.java的main方法中是如何捕获MethodAndArgsCaller异常的：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static void main(String argv[]) {
    ···
    	closeServerSocket();
	} catch (MethodAndArgsCaller caller) {
    	caller.run();//1
	} catch (RuntimeException ex) {
    Log.e(TAG, "Zygote died with exception", ex);
    closeServerSocket();
    throw ex;
	}    
}
```

当捕获到MethodAndArgsCaller异常时就会在注释1处调用MethodAndArgsCaller的run方法，MethodAndArgsCaller是Zygote.java的静态内部类：

frameworks/base/core/java/com/android/internal/os/Zygote.java

```java
public static class MethodAndArgsCaller extends Exception implements Runnable {
    private final Method mMtheod;
    private final String[] mArgs;
    public MethodAndArgsCaller(Method method, String[] args) {
        mMethod = method;
        mArgs = args;
    }
    public void run() {
        try {
            mMethod.invoke(null, new Object[] {mArgs});//1
        } catch (IllegalAccessException ex) {
            throw new RuntimeException(ex);
        }
        ···
    }
}
```

注释1处的mMethod指的是SystemServer的main方法，调用了mMethod的invoke方法后，SystemServer的main方法就会被动态调用，SystemServer进程就进入了SystemServer的main方法中。

#### 解析SystemServer进程

下面来查看SystemServer的main方法：

frameworks/base/services/java/com/android/server/SystemServer.java

```java
public static void main(String[] args) {
    new SystemServer().run();
}
```

main方法中只有调用了SystemServer的run方法：

framworks/base/services/java/com/android/server/SystemServer.java

```java
private void run() {
    try {
        ···
        //创建消息Looper
        Looper.prepareMainLooper();
        //加载了动态库libandroid_services.so
        System.loadLibrary("android_servers");//1
        performPendingShutdown();
        //创建系统的Context
        createSystemContext();
        mSystemServiceManager = new SystemServiceManager(mSystemContext);//2
        mSystemServiceManager.setRuntimeRestarted(mRuntimRestart);
        LocalService.addService(SystemServiceManager.class, mSystemServiceManager);
        SystemServerInitThreadPool.get();
    } finally {
        traceEnd();
    }
    try {
        traceBeginAndSlog("StartServices");
        //启动引导服务
        startBootstrapServices();//3
        //启动核心服务
        startCoreServices();//4
        //启动其他服务
        startOtherServices();//5
        SystemServerInitThreadPool.shutdown();
    } catch (Throwable ex) {
        Slog.e("System", "******************************************");
        Slog.e("System","************ Failure starting system services", ex);
        throw ex;
    } finally {
        traceEnd();
    }
    ···
}
```

在注释1处加载了动态库libandroid_servers.so。接下来在注释2处创建SystemServiceManager，它会对系统服务进行创建、启动和生命周期管理。在注释3处的startBootstrapServices方法中用SystemServiceManager启动了ActivityManagerService、PowerManagerService、PackageManagerService等服务。在注释4处的startCoreServices方法中则启动了DropBoxManagerService、BatteryService、UsageStatsService和WebViewUpdateService。在注释5处的startOtherServices方法中启动了CameraService、AlarmManagerService、VrManagerService等服务。这些服务的父类均为SystemService。从注释3、4、5的方法中可以看出，官方把系统服务分为三种类型，分别是引导服务、核心服务和其他服务，其中服务是一些非紧要和不需要立即启动的服务。这些系统服务总共有100多个，表中列出部分系统服务及其作用。

<img src="./Android进阶解密.assets/image-20231204153150469.png" alt="image-20231204153150469" style="zoom: 33%;" />

这些系统服务启动逻辑是相似的，这里以启动PowerManagerService来进行举例：

```java
mPowerManagerService = mSystemServiceManager.startService(PowerManagerService.class);
```

SystemServiceManager的startService方法启动了PowerManagerService：

frameworks/base/services/core/java/com/android/server/SystemServiceManager.java

```java
public void startService(@NonNull final SystemService service) {
    //注册Service
    mServices.add(service);//1
    long time = System.currentTimeMillis();
    try {
        //启动Service
        service.onStart();//2
    } catch (RuntimeException ex) {
        throw new RuntimeException("Failed to start service" + service.getClass().getName() + ":onStart threw an exception", ex);
    }
    warnIfTooLong(System.currentTimeMillis() - time, service, "onStart");
}
```

在注释1处将PowerManagerService添加到mService中，其中mService是一个存储SystemService类型的ArrayList，这样就完成了PowerManagerService的注册工作。在注释2处调用PowerManagerService的onStart函数完成启动PowerManagerService。

除了mSystemServiceManager的startService函数来启动系统服务外，也可以通过如下形式来启动系统服务，以PackageManagerService为例：

framework/base/services/core/java/com/android/server/pm/PackageManagerService.java

```java
public static PackageManagerService main(Context context, Installer installer, boolean factoryTest, boolean onlyCore) {
    //自检初始化的设置
    PackageManagerServiceCompilerMapping.checkProperties();
    PackageManagerService m = new PackageManagerService(context, installer, factoryTest, onlyCore);//1
    m.enableSystemUserPackages();
    ServiceManager.addService("package", m);//2
    return m;
}
```

在注释1直接创建PackageManagerService并在注释2处将PackageManagerService注册到ServiceManager中，ServiceManager用来管理系统中的各种Service，用于系统C/S架构中的Binder通信机制：Client端要使用某个Service，则需要先到ServiceManager查询Service的相关信息，然后根据Service的相关信息与Service所在的Server进程建立通信通路，这样Client端就可以使用Service了。

#### SystemServer进程总结

SystemServer进程被创建后主要做了如下工作：

1. 启动Binder线程池，这样就可以与其他进程进行通信
2. 创建SystemServiceManager，其用于对系统的服务进行创建、启动和生命周期管理
3. 启动各种系统服务



### Launcher启动过程

系统启动的最后一步是启动一个应用程序来显示系统中已经安装的应用程序，这个应用程序就叫做Launcher。Launcher在启动过程中会请求PackageManagerService返回系统中已经安装的应用程序的信息，并将这些信息封装成一个快捷图标列表显示在系统屏幕上，这样用户可以通过点击这些快捷图标来启动相应的应用程序。

通俗来讲Launcher就是Android系统的桌面，主要作用有两点：

1. 作为Android系统的启动器，用于启动应用程序。
2. 作为Android系统的桌面，用户显示和管理应用程序的快捷图标或者其他桌面组件。



#### Launcher启动过程

SystemServer在启动过程中会启动PackageManagerService，PackageManagerService启动后会将系统中的应用程序安装完成。在此之前已经启动的AMS会将Launcher启动起来，Launcher启动过程如图：

![image-20231205091806205](./Android进阶解密.assets/image-20231205091806205.png)

启动Launcher的入口为AMS的systemReady方法，它在SystemServer的startOtherServices方法中被调用：

frameworks/base/services/ajva/com/andorid/servere/SystemServer.java

```java
private void startOtherServices() {
    ···
    mActivityManagerService.systemReady(()-> {//1
        Slog.i(TAG,"Making services ready");
        traceBeginAndSlog("StartActivityManagerReadyPhase");
        mSystemServiceManager.startBootPhase(SystemService.PHASE_ACTIVITY_MANAGER_READY);
    	···
    }
    ···
}
```

与Android 7.1.2源码不同的是，Android 8.0的部分源码引入了Java Lambda表达式，比如注释1处。下面来看AMS的systemReady方法做了什么：

frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java

```java
public void systemReady(final Runnable goingCallback) {
    ···
    synchronized (this) {
        ···
        mStackSupervisor.resumeFocusedStackTopActivityLocked();
        mUsesrController.sendUserSwitchBroadcastLocked(-1,currentUserId);
    }
}
```

systemReady方法中调用了ActivityStackSupervisor的resumeFocusedStackTopActivityLocked方法：

frameworks/base/services/core/java/com/android/server/am/ActivityStackSupervisor.java

```java
boolean resumeFocusedStackTopActivityLocked(ActivityStack targetStack,ActivityRecord target, ActivityOptions targetOptions) {
    if (targetStack != null && isFoucusedStack(targetStack)) {
        return targetStack.resumeTopActivityUncheckedLocked(target,targetOptions);//1
    }
    ···
    return false;
}
```

在注释1处调用ActivityStack的resumeTopActivityUncheckedLocked方法，ActivityStack对象是用来描述Activity堆栈的，resumeTopActivityUncheckedLocked方法如下：

frameworks/base/services/core/java/com/android/server/am/ActivityStack.java

```java
boolean resumeTopActivityUncheckedLocked(ActivityRecord prev, ActivityOptions options) {
    if (mStackSupervisor.inResumeTopActivity) {
        return false;
    }
    boolean result = false;
    try {
        mStackSupervisor.inResumeTopActivity = true;
        result = resumeTopActivityInnerLocked(prev, options);//1
    } finally {
        mStackSupervisor.inResumeTopActivity = false;
    }
    mStackSupervisor.checkReadyForSleepLocked();
    return result;
}
```

在注释1处调用了resumeTopActivityInnerLocked方法：

frameworks/base/services/core/java/com/android/server/am/ActivityStack.java

```java
private boolean resumeTopActivityInnerLocked(Activity prev,ActivityOptions options) {
    ···
    return isOnHomeDisplay() && mStackSupervisor.resumeHomeStackTask(returnTaskType, prev, "prevFinished");
    ···
}
```

resumeTopActivityInnerLocked方法代码很长，在此仅截取我们要分析的关键部分，调用ActivityStackSupervisor的resumeHomeStackTask方法：

frameworks/base/services/core/java/com/android/server/am/ActivityStackSupervisor.java

```java
boolean resumeHomeStackTask(ActivityRecord prev, String reason) {
    ···
    if (r != null && !r.finishing) {
        moveFocusableActivityStackToFrontLocked(r, myReason);
        return resumeFocusedStackTopActivityLocked(mHomeStack, prev, null);
    }
    return mService.startHomeActivityLocked(mCurrentUser, myReason);
}
```

在resumeHomeStackTask方法中调用了AMS的startHomeActivityLocked方法：

frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java

```java
boolean startHomeActivityLocked(int userId, String reason) {
    //判断工厂模式和mTopAction的值，符合要求就继续执行下去
    if (mFactoryTest == FactoryTest.FACTORY_TEST_LOW_LEVEL && mTopAction == null) {//1
        return false;
    }
    //创建Launcher启动所需的Intent
    Intent intent = getHomeIntent();//2
    ActivityInfo aInfo = resolveActivityInfo(intent, STOCK_PM_FLAGS, userId);
    if (aInfo != null) {
        intent.setComponent(new ComponentName(aInfo.applicationInfo.packageName,aInfo.name));
        aInfo = new ActivityInfo(aInfo);
        aInfo.applicationInfo = getAppInfoForUser(aInfo.appllicationInfo, userId);
        ProcessRecord app = getProcessRecordLocked(aInfo.processName,aInfo.applicationInfo.uid,true);
        if (app == null || app.instr == null) {//3
            intent.setFlags(intent.getFlags() | Intent.FLAG_ACIVITY_NEW_TASK);
            final int resolvedUserId = UserHandle.getUserId(aInfo.applicationInfo.uid);
            final String myReason = reason + ":" + userId + ":" +resolvedUserId;
            //启动Launcher
            mActivityStarter.startHomeActivityLocked(intent, aInfo, myReason);//4
        }
    } else {
        Slog.wtf(TAG, "No home screen found for " + intent + new Throwable());
    }
    return true;
}
```

注释1处的mFactoryTest代表系统的运行模式，系统的运行模式分为三种，分别是非工厂模式、低级工厂模式和高级工厂模式，mTopAction则用来描述第一个被启动Activity组件的Action，它的默认值为Intent.ACTION_MAIN。因此注释 1 处的代码的意思就是mFactoryTest FactoryTest.FACTORY_TEST LOW_LEVEL （低级工厂模式）并且mTopAction 等于 null 时，直接返回 false 注释 处的 getHomelntent 方法如下所示：

frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java

```java
Intent getHomeIntent() {
    Intent intent = new Intent(mTopAction, mTopData!= null? Uri.parse(mTopData):null);
    intent.setComponent(mTopComponent);
    intent.addFlags(Intent.FLAG_DEBUG_TRIAGED_MISSING);
    if (mFactoryTest != FactoryTest.FACTORY_TEST_LOW_LEVEL) {
        intent.addCategory(Intent.CATEGORY_HOME);
    }
    return intent;
}
```

在getHomeIntent方法中创建了Intent，并将mTopAction和mTopData传入。mTopAction的值为Intent.ACTION_MAIN，并且如果系统运行模式不是低级工厂模式，则将intent的Category设置为Intent.CATEGORY_HOME，最后返回该Intent，再回到AMS的startHomeActivityLocked方法，假设系统的运行模式不是低级工厂模式，在注释3处判断符合Action为Intent.ACTION_MAIN、Category为Intent.CATEGORY_HOME的应用程序是否已经启动，如果没启动则调用注释4的方法启动该应用程序。这个这启动的应用程序就是Launcher，因为Launcher的AndroidManifest文件中的intent-filter标签匹配了Action为Intent.ACTION_MAIN，Category为Intent.CATEGORY_HOME。

Launcher的AndroidManifest文件如下所示：

packages/apps/Launcher3/AndroidManifest.xml

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.android.launcher3">
    <user-sdk android:targetSdkVersion="23" android:minSdkVersion="21" />
    ···
    <application ···>
        <activity android:name="com.android.launcher3.Launcher"
                  android:launchMode="singleTask"
                  android:clearTaskOnLaunch="true"
                  android:stateNoNeeded="true"
                  android:windowSoftInputMode="adjustPan | stateUnchanged"
                  android:screenOrientation="nosensor"
                  android:configChanges="keyboard|keyboardHidden|navigation"
                  android:resizeableActivity="true"
                  android:resumeWhilePausing="true"
                  android:taskAffinity=""
                  android:enable="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.HOME" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.MONKEY" />
            </intent-filter>
        </activity>
        ···
    </application>
</manifest>
```

可以看到intent-filter设置了android.intent.category.HOME属性，这样名称为com.android.launcher3.Launcher的Activity就成为了主Activity。从前面AMS的startHomeActivityLocked方法的注释4处，我们得知如果Launcher没有启动就会调用Activity的startHomeActivityLocked方法来启动Launcher：

frameworks/base/sevices/core/java/com/android/server/am/ActivityStarter.java

```java
void startHomeActivityLocked(Intent intent, ActivityInfo aInfo, String reason) {
    //将Launcher放入HomeStack中
    mSupervisor.moveHomeStackTaskToTop(reason);//1
    mlastHomeActivityStartResult = startActivityLocked(null /*caller*/,intent,
		null /*ephemeralIntent*/,null /*resolvedType*/,aInfo,null /*rInfo*/,
        null /*voiceSession*/,null /*voiceInteractor*/,null /*resultTo*/,
        null/*resultWho*/,0/*requestCode*/,0/*callingPid*/,0/*callingUid*/,
        null /*callingPackage*/,0/*realCallingPid*/,0/*realCallingUid*/,
        0/*startFlags*/,null /*options*/,false /*ignoreTargetSecurity*/,
        false /*componentSpecified*/,mLastHomeActivityStartRecord /*outActivity*/,
        null /*container*/,null /*inTask*/,"startHomeActivitv: " + reason);
    ···
}
```

在注释1处将Launcher放入HomeStack中，HomeStack是在ActivityStackSupervisor中定义的用于存储Launcher的变量。接着调用startActivityLocked方法来启动Launcher，剩余的过程会和Activity的启动过程类似，在第4章介绍。最终进入Launcher的onCreate方法中，到这里Launcher完成了启动。

#### Launcher中应用图标显示过程

Launcher 完成启动后会做很多的工作，作为桌面它会显示应用程序图标， 这与应用程序开发有所关联，应用程序图标是用户进入应用程序的入口，因此我们必要了解Launcher 是如何显示应用程序图标的。

先从Launcher的onCreate方法入手：

packages/apps/Launcher3/src/com/android/launcher3/Launcher.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ···
    LauncherAppState app = LauncherAppState.getInstance();//1
    mDeviceProfile = getResources().getConfiguration().orientation == Configuration.ORIENTATION_LANDSCAPE ? app.getInvariantDeviceProfile().landscapeProfile : app.getInvariantDeviceProfile().portraitProfile;
    mSharedPrefs = Utilities.getPrefs(this);
    mIsSafeModeEnabled = getPackageManager().isSafeMode();
    mModel = app.setLauncher(this);//2
    ···
    if (!mRestoring) {
        if (DISABLE_SYNCHRONOUS_BINDING_CURRENT_PAGE) {
            mModel.startLoader(PagedView.INVALID_RESTORE_PAGE);//3
        } else {
            mModel.startLoader(mWorkspace.getRestorePage());
        }
    }
    ···
}
```

在注释1处会调用LauncherAppState的实例，在注释2处调用它的setLauncher方法并将Launcher对象传入，LauncherAppState的setLauncher方法如下：

packages/apps/Launcher3/src/com/android/launcher3/LauncherAppState.java

```java
LauncherModel setLauncher(Launcher launcher) {
    getLocalProvider(mContext).setLauncherProviderChangeListener(launcher);
    mModel.initialize(launcher);//1
    return mModel;
}
```

在注释1处会调用LauncherModel的initialize方法：

packages/apps/Launcher3/src/com/android/launcher3/LauncherModel.java

```java
public void initialize(Callbacks callbacks) {
    synchronized (mLock) {
        unbindItemInfoAndClearQueueBindRunnables();
        mCallbacks = new WeakReference<Callbacks>(callbacks);
    }
}
```

在initialize方法中会将Callbacks，也就是传入的Launcher，封装成一个弱引用对象。因此我们得知mCallbacks变量指的就是封装成弱引用对象的Launcher，这个mCallbacks③后面会用到它。

> 由于Launcher实现了Callback接口。在mModel中，将传入的Launcher对象向下转型为Callback赋值给mCallbacks变量。并在LauncherModel中获得了一个Callbacks的软引用。通过这一过程，将Launcher对象作为Callback与mModel进行绑定，当mModel后续进行操作时，Launcher可以通过回调得到结果。

再回到Launcher的onCreate方法，在注释3处调用了LauncherModel的startLoader方法：

packages/apps/Launcher3/src/com/android/launcher3/LauncherModel.java

```java
···
//创建了具有消息循环的线程HandlerThread对象
@Thunk static final HandlerThread sWorkerThread = new HandlerThread("launcher-loader");//1
static {
    sWorkerThread.start();
}
@Thunk staticLoader(boolean isLaunching, int synchronousBindPage) {
    synchronized (mLock) {
        if (DEBUG_LOADERS) {
            Log.d(TAG, "startLoader isLaunching=" + isLaunching);
        }
        mDeferredBindRunnables.clear();
        if (mCallbacks != null && mCallbacks.get() != null) {
            isLaunching = isLaunching || stopLoaderLocked();
            mLoaderTask = new LoaderTask(mApp, isLaunching);//3
            if (synchronousBindPage > -1 && mAllAppsLoaded && mWorkspaceLoaded) {
                mLoaderTask.runBindSynchronousPage(synchronousBindPage);
            } else {
                sWorkerThread.setPriority(Thread.NORM_PRIORITY);
                sWorker.post(mLoaderTask);//4
            }
        }
    }
}
```

在注释1处创建了具有消息循环的线程HandlerThread对象。在注释2处创建了Handler，并且传入HandlerThread的Looper，这里Handler的作用就是向HandlerThread发送消息。在注释3处创建LoaderTask，在注释4处将LoaderTask作为消息发送给HandlerThread。

LoaderTask类实现了Runnable接口，当LoaderTask所描述的信息被处理时，则会调用它的run方法，LoaderTask是LauncherModel的内部类，代码如下：

packages/apps/Launcher3/src/com/android/launcher3/LauncherModel.java

```java
private class LoaderTask implements Runnable {
    ···
    public void run() {
        synchronized (mLock) {
            if (mStopped) {
                return;
            }
            mIsLoaderTaskRunning = true;
        }
        try {
            if (DEBUG_LOADERS) Log.d(TAG, "step 1.1:loading workspace");
            mIsLoadingAndBindingWorkspace = true;
            //加载工作区信息
            loadWorkspace();//1
            verifyNotStopped();
            if(DEBUG_LOADERS) Log.d(TAG, "step 1.2:bind workspace workspace");
            //绑定工作区信息
            bindWorkspace(mPageToBindFirst);//2
            if (DEBUG_LOADERS) Log.d(TAG, "step 1 completed, wait for idle");
            waitForIdle();
            verifyNotStopped();
            if(DEBUG_LOADERS) Log.d(TAG, "step 2.1:loading all apps");
            //加载系统已经安装的应用程序信息
            loadAllApps();//3
            ···
        } catch (CancellationException e) {
        } finally {
            ···
        }
    }
···
}
```

Launcher是用工作区的形式来显示系统安装的应用程序的快捷图标的，每一个工作区都是用来描述一个抽象桌面的，它由n个屏幕组成，每个屏幕又分为n个单元格，每个单元格用来显示一个应用程序的快捷图标。在注释1处和注释2处分别调用loadWorkspace方法和bindWorkspace方法来加载和绑定工作区信息。注释3处的loadAllApps方法用来加载系统已经安装的应用程序信息，代码如下：

packages/apps/Launcher3/src/com/android/launcher3/LauncherModel.java

```java
private void loadAllApps() {
    ···
    mHandler.post(new Runnable() {
        public void run() {
            final long bindTime = SystemClock.uptimMillis();
            final Callbacks callbacks = tryGetCallbacks(oldCallbacks);
            if (callbacks!=null) {
                callbacks.bindAllApplications(added);//1
                if (DEBUG_LOADERS) {
                    Log.d(TAG, "bound " + added.size() + " apps in" + (SystemClock.uptimeMillis() - bindTime) + "ms");
                }
            } else {
                Log.i(TAG, "not binding apps: no Launcher activity");
            }
        }
    });
    ···
}
```

在注释1处会调用callbacks的bindAllApplications方法，从之前的标注③处我们得知这个callbacks实际是指向Launcher的，下面我们来查看Launcher的bindAllApplications方法：

packages/apps/Launcher3/src/com/andorid/launcher3/Launcher.java

```java
public void bindAllApplications(final ArrayList<AppInfo> apps) {
    if (waitUntilResume(mBindAllApplicationsRunnable, true)) {
        mTmpAppList = apps;
        return;
    }
    if (mAppsView != null) {
        mAppView.setApps(apps);//1
    }
    if (mLauncherCallbacks != null) {
        mLauncherCallbacks.bindAllApplications(app);
    }
}
```

在注释1处会调用AllAppsContainerView类型的mAppsView对象的setApps方法，并将包含应用信息的列表apps传进去，AllAppsContainerView的setApps方法如下所示：

packages/apps/Launcher3/src/com/android/launcher3/allapps/AllAppsContainerView.java

```java
public void setApps(List<AppInfo> apps) {
    mApps.setApps(apps);
}
```

setApps方法会将包含应用信息列表apps设置给mApps，这个mApps是AlphabeticalAppsList类型的对象。接着查看AllAppsContainerView的onFinishInflate方法，如下所示：

packages/apps/Launcher3/src/com/android/launcher3/allapps/AllAppsContainerView.java

```java
@Override
protected void onFinishInflate() {
    super.onFinishInflate();
    ···
    mAppsRecyclerView = (AllAppsRecyclerView) findViewById(R.id.apps_list_view);//1
    mAppsRecyclerView.setApps(mApps);//2
    mAppsRecyclerView.setLayoutManager(mLayoutManager);
    mAppsRecyclerView.setAdapter(mAdapter);//3
    ···
}
```

onFinishInflate方法会在AllAppsContainerView加载完XML布局时调用，在注释1处得到AllAppsRecyclerView用来显示App列表，并在注释2处将次前的mApps设置进去，在注释3处为AllAppsRecyclerView设置Adapter。这样显示应用程序快捷图标的列表就会显示在屏幕上。

到这里Launcher中应用图标显示过程以及Launcher启动流程结束，接下来介绍Android系统启动流程。



#### Android系统启动流程

结合本章前4节的内容，我们可以清晰地总结出 Android 系统启动流程，这个流程主要有以下几个部分。

1. 启动电源以及系统启动
   当电源按下时引导芯片代码从预定义的地方(固化在 ROM)开始执行。加载引导程序BootLoader 到 RAM，然后执行。
2. 引导程序 BootLoader
   引导程序 BootLoader 是在 Android 操作系统开始运行前的一个小程序，它的主要作用是把系统OS 拉起来并运行。
3. Linux内核启动
   当内核启动时，设置缓存、被保护存储器、计划列表、加载驱动。当内核完成系统设置时，它首先在系统文件中寻找init.rc 文件，并启动 init 进程
4. init 进程启动
   初始化和启动属性服务，并且启动 Zygote进程
5. Zygote 进程启动
   创建 Java 虚拟机并为 Java 虚拟机注册JNI 方法，创建服务器端 Socket，启动SystemServer 进程。
6. SystemServer 进程启动
   启动 Binder 线程池和SystemServiceManager，并且启动各种系统服务。
7. Launcher启动
   被SystemServer进程启动的AMS会启动Launcher，Launcher启动后会将已安装应用的快捷图标显示到界面上。

结合上面流程给出Android系统启动流程图：

<img src="./Android进阶解密.assets/image-20231205161254714.png" alt="image-20231205161254714" style="zoom:33%;" />



## 应用程序进程启动

这里的“应用程序**进程**启动过程”区别于“应用程序启动过程（根Activity启动过程）”

要想启动一个应用程序，首先要保证这个应用程序所需要的应用程序进程已经启动。AMS在启动应用程序时会检查这个应用程序需要的应用程序进程是否存在，不存在就会请求 Zygote 进程启动需要的应用程序进程。在2.2节中，我们知道在 Zygote 的Java 框架层中会创建一个 Server端的 Socket，这个 Socket 用来等待AMS 请求 Zygote 来创建新的应用程序进程。Zygote进程通过fock自身创建应用程序进程,这样应用程序进程就会获得Zygote进程在启动时创建的虚拟机实例。当然，在应用程序进程创建过程中除了获取虚拟机实例外，还创建了 Binder 线程池和消息循环，这样运行在应用进程中的应用程序就可以方便地使用Binder 进行进程间通信以及处理消息了。





### 应用程序进程启动过程介绍

应用程序进程创建过程步骤较多，分为两个部分来讲解，分别是AMS发送启动应用进程请求，以及Zygote接受请求并创建应用程序进程。

#### AMS发送启动应用程序请求

![image-20231206105541527](./Android进阶解密.assets/image-20231206105541527.png)

AMS如果想要启动应用时序进程，就需要向Zygote进程发送创建应用程序的请求，AMS会通过startProcessLocked方法向Zygote进程发送请求：

frameworks/base/services/core/java/com/android/server/am/ActivityManagerService.java

```java
private final void startProcessLocked(ProcessRecord app, String hostingType, String hostingNameStr, String abiOverride, String entryPoint, String[] entryPointArgs) {
    ···
    try {
        try {
            final int userId = UserHandle.getUserId(app.uid);
            AppGlobals.getPackageManager().checkPackageStartable(app.info.packageName, userId);
        } catch (RemoteException e) {
            throw e.rethrowAsRuntimeException();
        }
        //获取要创建的应用程序的用户ID
        int uid = app.uid;//1
        int[] gids = null;
        int mountExternal = Zygote.MOUNT_EXTERNAL_NONE;
        if (!app.isolated) {
            ···
            /**
            * 2 对gids进行创建和赋值
            */
            if (ArrayUtils.isEmpty(perGids)) {
                gids = new int[3];
            } else {
                gids = new int[perGids.length+3];
                System.arraycopy(permGids,0,gids,permGids.length);
            }
            gids[0] = UserHandle.getSharedAppGid(UserHandle.getAppId(uid));
            gids[1] = UserHandle.getCacheAppGid(UserHandle.getAppId(uid));
            gids[2] = UserHandle.getUserGid(UserHandle.getUserId(uid));
        }
        ···
        if (entryPoint == null) entryPoint = "android.app.ActivityThread";//3
        Trace.traceBegin(Trace.TRACE_ACTIVITY_MANAGER, "Start proc: " + app.processName);
        checkTime(startTime, "startProcess: asking zygote to start proc");
        ProcessStartResult startResult;
        if (hostingType.equals("webview_service")) {
            startResult = startWebView(entryPoint, app.processName, uid, uid, git, debugFlags,mountExternal, app.info.targetSKDVersion, seInfo, requiredAbi, instructionSet, app.info.dataDir, null, entryPointArgs);
        } else {
            /**
            * 4 启动应用程序进程
            */
            startResult = Process.start(entryPoint,
				app.processName, uid, uid, gids, debugFlags, mountExternal, 
                app.info.targetSdkVersion, seInfo, requiredAbi, instructionSet, 
				app.info.dataDir, invokeWith, entryPointArgs);
        }
        ···
    } catch (RuntimeException e) {
        ···
    }
}
```

在注释1处得到创建应用程序进程的用户ID，在注释2处对用户组ID（gids）进行创建和赋值。在注释3处如果entryPoint为null，则赋值为android.app.ActivityThread，这个值就是应用程序进程主线程的类名。在注释4处调用Process的start方法，将此前得到的应用程序进程用户ID和用户组ID传进去，第一个参数entryPoint我们得知是android.app.ActivityThread，后面章节还会介绍。接下来查看Process的start方法：

frameworks/base/core/java/android/os/Process.java

```java
public static final ProcessStartResult start(final String processClass,
						final String niceName,
                        int uid, int gid, int[] gids,
                        int debugFlags, int moutExternal,
                        int targetSdkVersion,
                        String seInfo,
                        String abi,
                        String instructionSet,
                        String appDataDir,
                        String invokeWith,
                        String[] zygoteArgs) {
    return zygoteProcess.start(processClass, niceName, uid, gid, gids,
			debugFlags, mountExternal, targetSdkVersion, seInfo,
            abi, instructionSet, appDataDir, invokeWith, zygoteArgs);
}
```

在Process的start方法中调用了ZygoteProcess的start方法，其中ZygoteProcess类用于保持与Zygote进程的通信状态。该start方法如下：

frameworks/base/core/java/android/os/ZygoteProcess.java

```java
public final Process.ProcessStartResult start(final String processClass,
										final String niceName,
                                        int uid, int gid, int[] gids,
                                        int debugFlags, int mountExternal,
                                        int targetSdkVersion,
                                        String seInfo,
                                        String abi,
                                        String instructionSet,
                                        String appDataDir,
                                        String invokeWith,
                                        String[] zygoteArgs) {
    try {
        return startViaZygote(processClass, niceName, uid, gid, gids,
				debugFlags, mountExternal, targetSdkVersion, seInfo,
                abi, instructionSet, appDataDir, invokeWith, zygoteArgs);
    } catch (ZygoteStartWithFailedEx ex) {
        Log.e(LOG_TAG, "Starting VM process through Zygote failed");
        throw new RuntimeException ("Starting VM process through Zygote failed", ex);
    }
}
```

ZygoteProcess的start方法调用了startViaZygote方法：

frameworks/base/core/java/android/os/ZygoteProcess.java

```java
private Process.ProcessStartResult startViaZygote(final String processClass,final String niceName, final int uid, final intgid,final int[] gids,int debugFlags,int mountExternal,int targetSdkVersion,String seInfo,String abi,
String instructionSet,String appDataDir,String invokeWith,
String[] extraArgs)throws ZygoteStartFailedEx {
    /**
    * 1 创建字符串列表 argsForZygote,并将应用进程的启动参数保存在 argsForZygote 中
    */
	ArrayList<String> argsForZygote = new ArrayList<String>();
	argsForZygote.add("--runtime-args");
	argsForZygote.add("--setuid=" + uid);
	argsForZygote.add("--setgid=" + gid);
	if((debugFlags & Zygote.DEBUG_ENABLE_JNI_LOGGING)!= 0){
		argsForZygote.add("--enable-jni-logging");
    }
    ···
	synchronized(mLock){
		return zygoteSendArgsAndGetResult(openZygoteSocketIfNeeded(abi),argsForZygote);
    }
}
```

在注释1处创建了字符串列表argsForZygote，并将启动应用进程的启动参数保存在argsForZygote中，方法的最后会调用zygoteSendArgsAndGetResult方法，需要注意的是，zygoteSendArgsAndGetResult方法的第一个参数中调用了openZygoteSocketlfNeeded方法①，而第二个参数是保存应用进程的启动参数的argsForZygote。zygoteSendArgsAndGetResult方法如下所示：

frameworks/base/core/java/android/os/ZygoteProcess.java

```java
@GuardedBy("mLock")
private static Process.ProcessStartResult zygoteSendArgsAndGetResult(ZygoteState zygoteState, ArrayList<String>args) throws ZygoteStartFailedEx {
    try {
        int sz =args.size();
        for(int i=0;i<sz;i++){
            if(args.get(i).indexOf('\n')>=0){
                throw new ZygoteStartFailedEx("embedded newlines not allowed");
            }
        }
        final BufferedWriter writer =zygoteState.writer;
        final DataInputStream inputStream =zygoteState.inputStream;
        writer.write(Integer.toString(args.size()));
        writer.newLine();
        for(int i=0;i<sz;i++){
            String arg =args.get(i);
            writer.write(arg);
            writer.newLine();
        }
        writer.flush();
        Process.ProcessStartResult result =new Process.ProcessStartResult();
        result.pid =inputStream.readInt();
        result.usingWrapper =inputstream.readBoolean();
        if(result.pid<0){
            throw new ZygoteStartFailedEx("fork()failed");
        }
        return result;
    } catch (IOException ex){
        zygoteState.close();
        throw new ZygoteStartFailedEx(ex);
    }
}
```

zygoteSendArgsAndGetResult方法的主要作用就是将传入的应用进程的启动参数argsForZygote写入ZygoteState中，ZygoteState是ZygoteProcess的静态内部类，用于表示与Zygote进程通信的状态。结合前面的标注①我们知道ZygoteState其实是由openZygoteSocketIfNeeded方法返回的，那么我们接着来看openZygoteSocketIfNeeded方法做了什么：

frameworks/base/core/java/android/os/ZygoteProcess.java

```java
@GuardedBy("mLock")
private ZygoteState openZygoteSocketIfNeeded (String abi) throws ZygoteStartFailedEx {
    Preconditions.checkState(Thread.holdsLock(mLock),"ZygoteProcess lock not held");
    if(primaryZygoteState == null || primaryZygotestate.isClosed() {
        try {
            //与 Zygote 进程建立 Socket 连接
            primaryZygoteState = ZygoteState.connect(mSocket);//1
        } catch(IOException ioe){
            throw new ZygoteStartFailedEx("Error connecting to primary zygote", ioe);
        }
    }
    
    //连接Zygote主模式返回的ZygoteState是否与启动应用程序进程所需要的ABI匹配
    if(primaryZygoteState.matches(abi)) {//2
        return primaryZygoteState;
    }
    
    //如果不匹配，则尝试连接Zygote辅模式
    if(secondaryZygoteState == null || secondaryZygoteState.isClosed()) {
        try {
            secondaryZygoteState = ZygoteState.connect(mSecondarySocket);//3
        } catch(IOException ioe){
            throw new ZygoteStartFailedEx("Error connecting to secondary zygote",ioe);
        }
    }
       
    //连接Zygote辅模式返回的ZygoteState是否与启动应用程序进程所需要的ABI匹配
	if(secondaryZygoteState.matches(abi)) {//4
		return secondaryZygoteState;
    }
    
    throw new ZygoteStartFailedEx("Unsupported zygote ABI:" + abi);
}
```

在2.2节讲到Zygote进程启动过程时我们得知，在Zygote的main方法中会创建name为“zygote”的Server端Socket。在注释1处会调用ZygoteState的connect方法与名称为ZYGOTE_SOCKET的Socket建立连接，这里ZYGOTE_SOCKET的值为“zygote”，也就是说，在注释1处与Zygote进程建立Socket连接，并返回ZygoteState类型的primaryZygoteState对象，在注释2处如果primaryZygoteState与启动应用程序进程所需的ABI不匹配，则会在注释3处连接name为“zygote_secondary”的Socket。在2.2.2节中讲到过Zygote的启动脚本有4种，如果采用的是init.zygote32_64.rc或者init.zygote64_32.rc，则name为“zygote”的为主模式，name为“zygote_secondary”的为辅模式，那么注释2和注释3处的意思简单来说就是，如果连接 Zygote 模式返回的 ZygoteState 与启动应用程序进程所需的 ABI 不匹配， 连接 Zygote 辅模式。如果在注释4连接 Zygote 辅模式返回的 ZygoteState 与启动应用程序进程所需的 ABI 也不匹配， 则抛出 ZygoteStartFailedEx 异常。

#### Zygote接受请求并创建应用程序进程

![image-20231206143002345](./Android进阶解密.assets/image-20231206143002345.png)

Socket连接成功并匹配ABI后会返回ZygoteState类型对象，我们在分析zygoteSendArgsAndGetResult方法中讲过，会将应用进程的启动参数 argsForZygote 写入ZygoteState中，这样Zygote进程就会收到一个创建新的应用程序的请求，我们回到ZygoteInit的main方法：

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

这些内容在2.2.3讲过，这里在讲一遍。在注释1处通过registerZygoteSocket方法创建了一个Server端的Socket，这个name为“zygote”的Socket用来等待AMS请求Zygote，以创建新的应用程序进程，关于AMS后面的章节会进行介绍。在注释2处预加载类和资源。在注释3处启动SystemServer进程，这样系统的服务也会由SystemServer进程启动起来。在注释4处调用ZygoteServer的runSelectLoop方法来等待AMS请求创建新的应用程序进程。下面来看ZygoteServer的runSelectLoop方法：

frameworks/base/core/java/com/andorid/internal/os/ZygoteServer.java

```java
void runSelectLoop(String abiList) throws Zygote.MethodAndArgsCaller {
    ArrayList<FileDesciptor> fds = new ArrayList<FileDesciptor>();
    ArrayList<ZygoteConnection> peers = new ArrayList<ZygoteConnection>();//1
    fds.add(mServerSocket.getFileDesciptor());
    peers.add(null);
    //无线循环等待AMS请求
    while (true) {
        ···
        for (int i = pollFds.length-1; i>=0;--i) {
        	if ((pollFds[i].revents & POOLIN) == 0) {
                continue;
            }
            if (i==0) {
                ZygoteConnection newPeer = acceptCommandPeer(abiList);
                peers.add(newPeer);
                fds.add(newPeer.getFileDesciptor());
            } else {
                boolean done = peers.get(i).runOnce(this);//2
                if (done) {
                    peers.remove(i);
                    fds.remove(i);
                }
            }
        }
    }
}
```

当有AMS的请求数据到来时，会调用注释2处的代码，结合注释1处的代码，我们得知注释2处的代码其实是调用ZygoteConnection的runOnce方法来处理请求数据：

```java
boolean runOnce(ZygoteServer zygoteServer) throws Zygote.MethodAndArgsCaller {
    String args[];
    Arguments parseArgs = null;
    FileDescriptor[] descriptors;
    try {
        // 获取应用程序的启动参数
        args = readArgumentList();//1
        descriptors = mSocket.getAncillaryFileDescriptors();
        cloaseSocket();
        return true;
    }
    ···
    try {
        parsedArgs = new Arguments(args);//2
        ···
        /**
        * 3 创建应用程序进程
        */
        pid = Zygote.forkAndSpecialize(parsedArgs.uid, parsedArg.gid, parsedArgs.gids,
		parsedArgs.debugFlags, rlimits, parsedArgs.mountExternal, parsedArgs.seInfo,
        parsedArgs.niceName, fdsToClose, fdsToIgnore, parsedArgs.instructionSet,
        parsedArgs.appDataDir);
    } catch (ErrnoException ex) {
        ···
    }
    try {
        // 当前代码逻辑运行在子逻辑中
        if (pid == 0) {
            zygoteServer.closeServerSocket();
            IoUtils.closeQuietly(serverPipeFd);
            serverPipeFd = null;
            // 处理应用程序进程
            handleChildProc(parsedArgs, descriptors,childPipeFd, newStderr);
            return true;
        } else {
            IoUtils.closeQuietly(childPipeFd);
            childPipeFd = null;
            return handleParentProc(pid, desciptors, serverPipeFd, parsedArgs);
        }
    } finally {
        IoUtils.closeQuietly(childPipeFd);
        IoUtils.closeQuietly(serverPipeFd);
    }
}
```

在注释1处调用readArgumentList方法来获取应用程序进程的启动参数，并在注释2处将readArgumentList方法返回的字符串数组args封装到Arguments类型的parsedArgs对象中。在注释3处调用Zygote的forkAndSpecial方法来创建应用程序进程，参数为parsedArgs中存储的应用进程启动参数，返回值为pid。forkAndSpecialize方法主要是通过fork当前进程来创建一个子进程的，如果pid等于0，则说明当前代码逻辑运行在新创建的子进程（应用程序进程）中，这是就会调用handleChildProc方法来处理应用程序进程：

frameworks/base/core/java/com/android/internal/os/ZygoteConnection.java

```java
private void handleChildProc(Arguments parsedArgs, FileDescriptor[]descriptors, FileDescriptor pipeFd, PrintStreamnewStderr) throws Zygote.MethodAndArgsCaller {
    ···
    if(parsedArgs.invokeWith !=null) {
        WrapperInit.execApplication(parsedArgs.invokeWith,
				parsedArgs.niceName,parsedArgs.targetSdkVersion,
                VMRuntime.getCurrentInstructionSet(),
                pipeFd,parsedArgs.remainingArgs);
    } else {
        ZygoteInit.zygoteInit(parsedArgs.targetSdkVersion,
				parsedArgs.remainingArgs,null /*classLoader */);
    }
}
```

handleChildProc方法中调用了ZygoteInit的zygoteInit方法：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static final void zygoteInit(int targetSdkVersion,String[]argv,
		ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    if(RuntimeInit.DEBUG) {
        Slog.d(RuntimeInit.TAG,"RuntimeInit:Starting application from zygote");
    }
    Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER,"ZygoteInit");
    RuntimeInit.redirectLogStreams();
    RuntimeInit.commonInit();
    ZygoteInit.nativeZygoteInit();//1
    RuntimeInit.applicationInit(targetSdkVersion,argv,classLoader);//2
}
```

在注释1处会在新创建的应用程序进程中创建Binder线程池，这将在3.3节详细介绍。在注释2处调用了RuntimeInit的applicationInit方法：

handleChildProc方法中调用了ZygoteInit的zygoteInit方法：

frameworks/base/core/java/com/android/internal/os/RuntimeInit.java

```java
protected static void applicationInit(int targetSdkVersion,String[]argv,ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    ···
    final Arguments args;
    try {
        args = new Arguments(argv);
    } catch (IllegalArgumentException ex) {
        Slog.e(TAG,ex.getMessage());
        return;
    }
    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
    invokeStationMain(args.startClass, args.startArgs, classLoader);//1
}
```

在applicationInit方法中会在注释1处调用invokeStaticMain方法，需要注意的是，第一个参数args.startCalss，它指的就是本章开头提到的参数android.app.ActivityThread。接下来来看invokeStaticMain方法：

frameworks/base/core/java/com/android/internal/os/RuntimeInit.java

```java
private static void invokeStaticMain(String className, String[]argv, ClassLoaderclassLoader) throws Zygote.MethodAndArgsCaller {
    Class<?> cl;
    try {
        //获得android.app.ActivityThread类
        cl =Class.forName(className,true,classLoader);//1
    } catch (ClassNotFoundException ex){
        throw new RuntimeException(
            "Missing class when invoking static main "+className,
            ex);
    }
    Method m;
    try {
        //获得ActivityThread的main方法
        m=cl.getMethod("main",new Class[]{String[].class });//2
    } catch (NoSuchMethodException ex){
        throw new RuntimeException(
            "Missing static main on "+className,ex);
    }
    ···
    throw new Zygote.MethodAndArgsCaller(m,argv);//3
}
```

可以看到在注释1处通过反射获得了android.app.ActivityThread类，接下来在注释2处获得了ActivityThread的main方法，并将main方法传入注释3处的Zygote中的MethodAndArgsCaller类的构造方法中。在注释3处抛出的MethodAndArgsCaller异常会被Zygote的main方法捕获，至于这里为何采用了抛出异常而不是直接调用ActivityThread的main方法，原理和本书2.3.1节Zygote处理SystemServer进程是一样的，这种抛出异常的处理会清除所有的设置过程需要的堆栈帧，并让ActivityThread的main方法看起来像是应用程序进程的入口方法。下面来查看Zygotelnit.java的main方法是如何捕获MethodAndArgsCaller异常的，如下所示：

frameworks/base/core/java/com/android/internal/os/Zygotelnit.java

```java
public static void main(String argv[]){
    ···
        closeServerSocket();
	} catch (MethodAndArgsCaller caller){
    	caller.run();//1
	} catch(RuntimeException ex){
    	Log.e(TAG,"Zygote died with exception",ex);
    	closeServerSocket();
    	throw ex;
	}
}
```

当捕获到MethodAndArgsCaller异常时，就会在注释1处调用MethodAndArgsCaller的run方法，MethodAndArgsCaller是Zygote.java的静态内部类：

frameworks/base/core/java/com/android/internal/os/Zygote.java

```java
public static class MethodAndArgsCaller extends Exceptionimplements Runnable {
    private final Method mMethod;
    private final String[]mArgs;
    public MethodAndArgsCaller(Method method,String[]args){
        mMethod =method;
        mArgs =args;
    }
    public void run(){
        try {
            mMethod.invoke(null,new Object[]{mArgs });//1
        }catch(IllegalAccessException ex){
            throw new RuntimeException(ex);
        }
        ···
    }
}
```

注释1处的mMethod指的就是ActivityThread的main方法，调用了mMethod的invoke方法后，ActivityThread的main方法就会被动态调用，应用程序进程就进入了ActivityThread的main方法中。讲到这里，应用程序进程就创建完成了并且运行了主线程的管理类ActivityThread。



### Binder线程池启动过程

在3.2.2节中学习了Zygote接收请求并创建应用程序进程，其中在应用程序进程创建过程中会启动Binder线程池，我们来查看ZygoteInit类的zygoteInit方法：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static final void zygoteInit(int targetSdkVersion,String[]argv,
		ClassLoader classLoader) throws Zygote.MethodAndArgsCaller {
    if(RuntimeInit.DEBUG) {
        Slog.d(RuntimeInit.TAG,"RuntimeInit:Starting application from zygote");
    }
    Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER,"ZygoteInit");
    RuntimeInit.redirectLogStreams();
    RuntimeInit.commonInit();
    ZygoteInit.nativeZygoteInit();//1
    RuntimeInit.applicationInit(targetSdkVersion,argv,classLoader);
}
```

在注释1处会在新创建的应用程序进程中创建Binder线程池，下面来查看nativeZygoteInit方法：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
private static final native void nativeZygoteInit();
```

很明显nativeZygoteInit是一个JNI方法，它对应的函数是什么呢？在AndroidRuntime.cpp的JNINativeMethod数组中得知它对应的函数是com_android_internal_os_ZygoteInit_nativeZygoteInit：

framworks/base/core/jni/AndroidRuntime.cpp

```cpp
const JNINativeMethod methodsp[] = {
    { "nativeZygoteInit", "(V)", 
    	(void*) com_android_internal_os_ZygoteInit_nativeZygoteInit},
};
```

接下来来查看com_android_internal_os_ZygoteInit_nativeZygoteInit函数：

frameworks/base/core/jni/AndroidRuntime.cpp

```cpp
static void com_android_internal_os_ZygoteInit_nativeZygoteInit(JNIEnv* env, jobject clazz)
{
    gCurRuntime -> onZygoteInit();
}
```

gCurRuntime是AndroidRuntime类型的指针，它是在AndroidRuntime初始化时就创建的：

frameworks/base/core/jni/AndroidRuntime.cpp

```cpp
···
static AndroidRuntime* gCurRuntime = NULL;
···
AndroidRuntime::AndroidRuntime(char* argBlockStart, const size_t argBlockLength) : 
	mExitWithoutCleanup(false),
	mArgBlockStart(argBlockStart),
	mArgBlockLength(argBlockLength)
{
    ···
    gCurRuntime = this;
}
```

AppRuntime继承自AndroidRuntime，AppRuntime创建时就会调用AndroidRuntime的构造函数，gCurRuntime就会被初始化，它指向的是AppRuntime，我们来查看AppRuntime的onZygoteInit函数，AppRuntime在app_main.cpp中实现：

frameworks/base/cmds/app_process/app_main.cpp

```cpp
virtual void onZygoteInit()
{
    sp<ProcessState> proc = ProcessState::self();
    ALOGV("App process: starting thread pool.\n");
    proc -> startThreadPool();
}
```

最后一行会调用ProcessState的startThreadPool函数来启动Binder线程池：

frameworks/native/libs/binder/ProcessState.cpp

```cpp
void ProcessState::startThreadPool()
{
    AutoMutex _l(mLock);
    if (!mThreadPoolStarted) {//1
        mThreadPoolStarted = true;//2
        spawnPooledThread(true);
    }
}
```

**支持Binder通信的进程中都有一个ProcessState类**，它里面有一个mThreadPoolStated变量，用来表示Binder线程池是否已经被启动过，默认值为false。在每次调用startThreadPool函数时都会在注释1处先检查这个标记，从而确保Binder线程池只会被启动一次。如果Binder线程池未被启动，则在注释2处设置mThreadPoolStarted为true，并调用spawnPooledThread函数来创建线程池中的第一个线程，也就是线程池的主线程：

frameworks/native/libs/binder/ProcessState.cpp

```cpp
void ProcessState::spawnPooledThread(bool isMain)
{
    if (mThreadPoolStarted) {
        String8 name = makeBinderThreadName();
        ALOGV("Spawning new pooled thread, name=%s\n", name.string());
        sp<Thread> t = new PoolThread(isMain);
        t->run(name.string());//1
    }
}
```

可以看到Binder线程为一个PoolThread。在注释1处调用PoolThread的run函数启动一个新的线程。下面来看PoolThread类：

frameworks/native/libs/binder/ProcessState.cpp

```cpp
class PoolThread : public Thread
{
··
protected:
	virtual bool threadLoop()
    {
        IPCThreadState::self()->joinThreadPool(mIsMain);//1
        return false;
    }
    const bool mIsMain;
}
```

PoolThread类继承了Thread类。在注释1处调用IPCThreadState的joinThreadPool函数，将当前线程注册到Binder驱动程序中，这样我们创建的线程就加入了Binder线程池中，新创建的应用程序进程就支持Binder进程间通信了，我们**只需要创建当前进程的Binder对象，并将它注册到ServiceManager中就可以实现Binder进程间通信**，而不必关心进程间是如何通过Binder进行通信的。



### 消息循环创建过程

Zygote接收请求并创建应用程序进程，应用程序进程启动后会创建消息循环。我们回到RuntimeInit的invokeStaticMain方法：

frameworks/base/core/java/com/android/internal/os/RuntimeInit.java

```java
private static void invokeStaticMain(String className, String[]argv, ClassLoaderclassLoader) throws Zygote.MethodAndArgsCaller {
    Class<?> cl;
    ···
    throw new Zygote.MethodAndArgsCaller(m,argv);//3
}
```

invokeStaticMain方法已经在3.2讲过，这里主要看最后一行，会抛出一个MethodAndArgsCaller异常，这个异常会被ZygoteInit的main方法捕获：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static void main(String argv[]) {
    ···
    try {
        ···
    } catch (MethodAndArgsCaller caller) {
        caller.run();//1
    } catch (RuntimeException ex) {
        Log.e(TAG, "Zygote died with exception", ex);
        closeServerSocket();
        throw ex;
    }  
}
```

在注释1处捕获到MethodAndArgsCaller时会执行caller的run方法：

frameworks/base/core/java/com/android/internal/os/ZygoteInit.java

```java
public static class MethodAndArgsCaller extends Exception implements Runnable {
    private final Method mMethod;
    private final String[] mArgs;
    public MethodAndArgsCaller(Method method, String[] args) {
        mMethod = method;
        mArgs = args;
    }
    public void run() {
        try {
            mMethod.invoke(null, new Object[] {mArgs});//1
        } catch (IllegalAccessException ex) {
            throw new RuntimeException(ex);
        }
        ···
        	throw new RuntimeException(ex);
    	}
    }
}
```

在3.2.2节我们得知，mMethod指的就是ActivityThread的main方法，mArgs指的是应用程序进程的启动参数。在注释1处调用ActivityThread的main方法：

frameworks/base/core/java/android/app/ActivityThread.java

```java
public static void main(String[] args) {
    ···
    // 创建主线程Looper
    Looper.prepareMainLooper();//1
    ActivityThread thread = new ActivityThread();//2
    thread.attach(false);
    if (sMainThreadHandler == null) {//3
        // 创建主线程H类
        sMainThreadHandler = thread.getHandler();//4
    }
    if (false) {
        Looper.myLooper().setMessageLogging(new LogPrinter(Log.DEBUG,"ActivityThread"));
    }
    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
    // Looper开始工作
    Looper.loop();//5
    throw new RuntimeException("Main thread loop unexpectedly exited");
}
```

ActivityThread类用于管理当前应用程序的主线程，在注释1处创建主线程的消息循环Looper，在注释2处创建ActivityThread。在注释3处判断Handler类型的sMainThreadHandler是否为null，如果为null则在注释4处获取H类并赋值给sMainThreadHandler，这个H类继承自Handler，是ActivityThread的内部类，用于处理主线程的消息循环，在第4章、第5章我们将会经常提到它。在注释5处调用Looper的loop方法，使得Looper开始处理消息。可以看出，系统在应用程序进程启动完成后，就会创建一个消息循环，这样运行在应用程序进程中的应用程序可以方便地使用消息处理机制。

