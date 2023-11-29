## 0x1、名词

### AOSP

[掘金](https://juejin.cn/post/7038543675109933070?searchId=20231127145609219A7F127C0B34C2052D#heading-0)

Android Open Source Projecy，安卓系统开源源码包，Android移动终端平台（高通、MTK等）基于原生的Android代码进行更改，形成自己的平台代码，其他手机厂商再根据平台代码提供自己的移动终端解决方案（系统定制）。

aosp源码编译后会生成一系列的产物（out目录下）：

+ /out/host → Android开发工具相关的产物，，包含各种SDK工具，如adb、dex2oat、aapt等；
+ /out/target/common → 一些通用的编译产物，包含Java应用代码和Java库；
+ /out/target/product/[product_name] → 针对特定设备的编译产物，以及平台相关C/C++代码与二进制文件（如system.image、ramdisk.img、userdata.img、boot.img等；

我们平时在AS里看到的源码库`android.jar`是打包后的`classes.jar`，详细路径：out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/classes.har。

以前在一些场景，需要使用SDK中隐藏的API（@hide注解），一种解法就是用aosp编译生成的classes.jar替换原生sdk中的android.jar。



### Gerrit

Android使用Git作为代码管理工具，开发了`Gerrit`进行代码审核，一遍更好地对代码进行集中式管理。实际上就是一个Git服务器，他为再起服务器上托管的Git仓库提供了一系列权限控制，以及一个作用域Code Review的Web页面。



### Repo

Repo命令行工具，用Python对Git命令进行封装，简化对多个Git库的集中式管理、





### 0x5、编译

一些名词解释

+ Makefile → Android平台编译系统，用Makefile写出来的一个独立项目，定义了编译规则，实现自动化编译，将分散在数百个Git库中的代码整合起来，统一编译，而且把产物分门别类地输出到一个目录，打包成手机ROM，还可以生成应用开发时使用的SDK、NDK等。
+ Android.mk → 定义一个模块的必要参数，使模块随着平台编译，简单点说就是告诉系统以什么规则编译源代码，并生成对应目标文件；
+ kati →  Google专门为Android研发的小工具，基于Golang和C++，作用是：将Android中的Makefile转换为Ninja文件
+ Ninja → 致力于速度的小型编译系统，把Makefile看做高级语言，那它就是汇编，文件后缀为.ninja；
+ Android.bp → 替换Android.mk的配置文件；
+ Blueprint → 解析Android.bp文件翻译成Ninja语法文件；
+ Soong → Makefile编译系统的替代品，负责解析Android.bp文件，并将之转换为Ninja文件；

> Android工程越来越大，Makefile编译耗时越来越长，Android 7.0引入速度和并行效率更佳的Ninja来编译系统；
>
> Soong 借助 Blueprint定义的Android.bp语法，完成Android.bp的解析，最终转换成Ninja文件;
>
> Makefile文件(.make或.mk)通过kati转换为Ninja文件(.ninja);
>
> Makefile是设计来给开发编写的，而Ninja则是设计给其他程序生成的，可类比做高级语言和汇编语言；



