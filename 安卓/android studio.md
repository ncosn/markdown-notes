## AndroidStudio新变化

### 实时编辑

> **重要提示** ：此功能尚处于积极开发阶段，因此您可能会遇到一些不稳定的行为。在 Android Studio Giraffe 及更高版本中，它需要 Jetpack Compose Runtime 1.3.0 或更高版本以及 AGP 8.1 或更高版本。Google 一直在努力改进此功能，欢迎您提供反馈。 如果您发现问题，[请向我们报告](https://issuetracker.google.com/issues/new?component=192708&%3Btemplate=840533&hl=zh-cn)。 添加来自 Logcat 的信息以及对您所做代码更改的说明。您还可以查看[待解决问题的列表](https://issuetracker.google.com/issues?q=status%3Aopen+componentid%3A1189787&%3Bs=created_time%3Adesc&hl=zh-cn)。

1. Android Studio Giraffe或更高版本，并且实体设备或模拟器的API级别不低于30
2. 在 Windows 或 Linux 上，依次前往 **File** > **Settings** > **Editor** > **Live Edit**。
3. 点击**Run**图标以部署应用
4. 开启实时编辑功能后，**Running Devices** 工具窗口的右上角会显示 **Up-to-date** 绿色对勾标记

#### 排查实时编辑问题

如果在测试设备上没有看到修改效果，则说明 Android Studio 可能未能更新您的修改。请检查实时编辑指示器是否显示 **Out Of Date**（如图 8 所示），这表示发生了编译错误。如需了解有关错误的信息以及有关如何解决该错误的建议，请点击指示器。



#### 实时编辑功能的限制

以下是现有限制的列表。

- [仅适用于 Android Studio Giraffe 及更高版本] 实时编辑功能需要 [Compose Runtime 1.3.0 或更高版本](https://developer.android.google.cn/jetpack/compose/bom/bom-mapping?hl=zh-cn)。如果您的项目使用较低版本的 Compose，系统会停用实时编辑功能。
- [仅适用于 Android Studio Giraffe 及更高版本] 实时编辑功能需要 AGP 8.1 或更高版本。如果您的项目使用较低版本的 AGP，系统会停用实时编辑功能。
- 实时编辑功能要求实体设备或模拟器搭载 API 级别为 30 或更高级别的操作系统。
- 实时编辑功能仅支持修改函数正文，也就是说，您无法进行下列操作：更改函数名称或签名、添加或移除函数、更改非函数字段。
- 当您首次在文件中更改 Compose 函数时，实时编辑会重置应用的状态。这仅在第一次代码更改后才会发生，即您对该文件中的 Compose 函数进行的后续代码更改不会重置应用状态。
- 使用实时编辑功能修改过的类可能会导致性能下降。如果要[评估应用的性能](https://developer.android.google.cn/studio/profile/measuring-performance?hl=zh-cn)，请使用干净的发布 build 运行应用。
- 您必须执行完整的运行，以便调试程序在使用实时编辑修改过的类上运行。
- 使用实时编辑修改正在运行的应用，可能会导致应用崩溃。如果发生这种情况，可以使用 **Run** 按钮重新部署应用。
- 实时编辑不会执行项目 build 文件中定义的任何字节码处理，例如，使用 **Build** 菜单中的选项或点击 **Build** 或 **Run** 按钮构建项目时要应用的字节码处理。
- 非可组合函数会在设备或模拟器上实时更新，并且会触发完全重组。完全重组可能不会调用更新后的函数。对于非可组合函数，您必须触发新更新的函数或再次运行应用。
- 应用重启后，实时编辑不会自动恢复。您必须再次运行应用。
- 实时编辑功能仅支持可调试的进程。
- 实时编辑不支持在 build 配置中的 `kotlinOptions` 下为 `moduleName` 使用自定义值的项目。
- 实时编辑功能不适用于多部署部署。这意味着，您不能先部署到一台设备，然后再部署到另一个设备。实时编辑功能仅在应用部署到的最后一组设备上有效。
- 实时编辑功能支持多设备部署（部署到通过目标设备下拉菜单中的 **Select multiple devices** 创建的多台设备）。不过，该功能并非官方支持的功能，因此可能会出现问题。如果您遇到问题，[请报告](https://issuetracker.google.com/issues/new?component=192708&%3Btemplate=840533&hl=zh-cn)。



### Android Gradle插件和Android Studio兼容性

| Android Studio 版本      | 所需插件版本 |
| ------------------------ | ------------ |
| Flamingo \| 2022.2.1     | 3.2-8.0      |
| Electric Eel \| 2022.1.1 | 3.2-7.4      |
| Dolphin \| 2021.3.1      | 3.2-7.3      |
| Chipmunk \| 2021.2.1     | 3.2-7.2      |
| Bumblebee \| 2021.1.1    | 3.2-7.1      |
| Arctic Fox \| 2020.3.1   | 3.1-7.0      |



## 管理项目

### 项目结构

Android Studio 中的每个项目都包含一个或多个内有源代码文件和资源文件的模块。模块的类型包括：

- Android 应用模块
- 库模块
- Google App Engine 模块

默认情况下，Android Studio 会在 Android 项目视图中显示您的项目文件（如图 1 所示）。该视图按模块组织结构，方便您快速访问项目的关键源文件。所有 build 文件都在顶层的 **Gradle Scripts** 下显示。

每个应用模块都包含以下文件夹：

- **manifests**：包含 `AndroidManifest.xml` 文件。
- **java**：包含 Kotlin 和 Java 源代码文件，包括 JUnit 测试代码。
- **res**：包含所有非代码资源，例如界面字符串和位图图像。

> **注意**：不同的模块类型具有不同的组结构。例如，原生模块在 `main` 组内还包含 `cpp/` 文件夹，而 Kotlin 或 Java 库不包含 `manifests` 或 `res` 组。



### Gradle构建系统

Android Studio会将Gradle用作构建系统的基础，并通过Android Gradle插件提供更多面向Android的功能。该构建系统可以作为集成工具从Android Studio菜单运行，也可从命令行独立运行。可以利用构建系统的功能执行一下操作：

+ 自定义、配置和扩展构建流程。
+ 使用相同的项目和模块为您的应用创建多个具有不同功能的APK。
+ 在不同源集中重复使用代码和资源。

利用Gradle的灵活性，您可以在不修改应用核心源文件的情况下完成以上所有操作。

如果您使用Kotlin，则Android Studio build文件名为`build.gradle.kts`；如果您使用Groovy，则名为`build.gradle`。它们是使用Android Gradle插件提供的元素以Kotlin或Groovy语法配置build的纯文本文件。每个项目都有一个用于整个项目的顶级build文件，以及用于各模块的单独模块级build文件。在导入现有项目时，Android Studio会自动生成必要的build文件。

> **注意：**我们可能会在文档中仅引用 `build.gradle.kts` 或 `build.gradle` 文件，但它们在概念上可以互换。例如，如果您看到 `build.gradle.kts`，但您使用 Groovy DSL 配置 build，则可以将其视作 `build.gradle` 文件（反之亦然）。



#### build变体

构建系统可以帮助您从一个项目创建同一应用的不同版本。如果您同时拥有免费版本和付费版本的应用，或想要在Google Play上为不同设备配置分发多个APK，此功能十分实用



### 项目结构设置

如需更改 Android Studio 项目的各种设置，请依次点击 **File > Project Structure**，打开 **Project Structure** 对话框。该对话框包含以下各部分：

- **Project**：设置 [Gradle 和 Android Gradle 插件](https://developer.android.google.cn/studio/build?hl=zh-cn#build-files)的版本以及代码库位置名称。

- **SDK Location**：设置项目使用的 JDK、Android SDK 和 Android NDK 的位置。

- **Variables**：可让您修改 build 脚本中使用的变量。

- Modules

  ：可让您修改特定于模块的 build 配置，包括目标和最低 SDK、应用签名以及库依赖项。每个模块的设置页面都分成以下标签页：

  - **Properties**：指定编译模块所用的 SDK 和构建工具的版本。
  - **Signing**：指定用于[为应用签名](https://developer.android.google.cn/tools/publishing/app-signing?hl=zh-cn#sign-auto)的证书。

- **Dependencies**：列出该模块的库、文件和模块依赖项。您可以在此窗格中添加、修改和删除依赖项。如需详细了解模块依赖项，请参阅[配置 build 变体](https://developer.android.google.cn/tools/building/configuring-gradle?hl=zh-cn#declareDeps)。

  **预览**：如果您使用的是版本目录，请注意，通过 **Project Structure** 对话框添加的依赖项会添加到模块的 `build.gradle` 文件而非目录。如需详细了解 Android Studio 中的 Gradle 版本目录支持，请参阅[预览版版本说明](https://developer.android.google.cn/studio/preview/features?hl=zh-cn#gradle-version-catalogs)。

- **build 变体**：可让您为项目配置不同的变种和 build 类型。

  - **变种**：可让您创建多个 build 变种，其中的每个变种用于指定一组配置设置，例如模块的最低和目标 SDK 版本以及[版本代码和版本名称](https://developer.android.google.cn/studio/publish/versioning?hl=zh-cn#versioningsettings.html)。

    例如，您可以定义两个变种，一个变种的最低 SDK 为 21、目标 SDK 为 29，另一个变种的最低 SDK 为 24、目标 SDK 为 33。

  - **build 类型**：可让您创建和修改 build 配置，如[配置 build 变体](https://developer.android.google.cn/tools/building/configuring-gradle?hl=zh-cn)中所述。默认情况下，每个模块都有“debug”和“release”这两种 build 类型，但您也可以根据需要定义更多 build 类型。



### 配置IDE

#### 自定义虚拟机选项

通过 `studio.vmoptions` 文件，您可以自定义 Android Studio 的 JVM 选项。为了提高 Studio 的性能，最常用的调节选项是最大堆大小，但您也可以使用 `studio.vmoptions` 文件替换其他默认设置（例如初始堆大小、缓存大小和 Java 垃圾回收开关）。

如需创建新的 `studio.vmoptions` 文件或打开现有文件，请按以下步骤操作：

1. 依次点击 **Help** > **Edit Custom VM Options**。如果您之前从未修改过 Android Studio 的虚拟机选项，Android Studio 将提示您新建一个 `studio.vmoptions` 文件。点击 **Yes** 以创建文件。
2. `studio.vmoptions` 文件会在 Android Studio 的编辑器窗口中打开。 修改该文件以添加您自己的自定义虚拟机选项。如需可自定义 JVM 选项的完整列表，请参阅 Oracle 的 [Java HotSpot 虚拟机选项](http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html)页面。

您创建的 `studio.vmoptions` 文件将添加到默认的 `studio.vmoptions` 文件中，后者位于 Android Studio 安装文件夹内的 `bin/` 目录中。

请注意，切勿直接修改 Android Studio 程序文件夹内的 `studio.vmoptions` 文件。尽管您可以访问该文件以查看 Studio 的默认虚拟机选项，但仅修改自己的 `studio.vmoptions` 文件可确保您不会替换 Android Studio 的重要默认设置。因此，在您的 `studio.vmoptions` 文件中，请仅替换您关注的属性，以便 Android Studio 可继续为您未更改的所有属性使用默认值。

**最大堆大小**

默认情况下，Android Studio 的最大堆大小为 1280MB。如果您处理的是大项目，或者您的系统有大量 RAM 可用，您可以通过增大 Android Studio 进程（例如核心 IDE、Gradle 守护程序和 Kotlin 守护程序）的最大堆大小以提升性能。

Android Studio 会自动检查可采取的堆大小优化措施，并在检测到性能可以提升时通知您。

> **注意**：分配过多内存会降低性能。



### 导出和导入 IDE 设置

您可以导出一个 `Settings.jar` 文件，其中包含项目的全部或部分首选 IDE 设置。然后，您可以将该 JAR 文件导入其他项目，并/或将该文件共享给同事，以便他们将其导入到自己的项目中。



### 设置 JDK 版本

Android Studio 2.2 及更高版本捆绑提供了最新版本的 OpenJDK，这是我们建议用于 Android 项目的 JDK 版本。如需使用捆绑的 JDK，请执行以下操作：

1. 在 Android Studio 中打开您的项目，然后依次选择 **File > Settings... > Build, Execution, Deployment > Build Tools > Gradle**（在 Mac 上，依次选择 **Android Studio > Preferences... > Build, Execution, Deployment > Build Tools > Gradle**）。
2. 在 **Gradle JDK** 下，选择 **Embedded JDK** 选项。
3. 点击 **OK**。

默认情况下，用于编译项目的 Java 语言版本取决于项目的 [`compileSdkVersion`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.BaseExtension.html#com.android.build.gradle.BaseExtension:compileSdkVersion)（因为不同版本的 Android 支持不同版本的 Java）。如有必要，您可以通过将以下 [`CompileOptions {}`](https://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.CompileOptions.html) 代码块添加到 `build.gradle` 文件替换此默认 Java 版本：

```groovy
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION\_1\_6
        targetCompatibility JavaVersion.VERSION\_1\_6
    }
}
```





### 向您的项目添加 C 和 C++ 代码

将 C 和 C++ 代码置于项目模块的 `cpp` 目录中，以将其添加到您的 Android 项目中。在您构建项目时，这些代码会编译到一个可由 Gradle 与您的应用打包在一起的原生库中。然后，Java 或 Kotlin 代码即可通过 Java 原生接口 (JNI) 调用原生库中的函数。如需详细了解如何使用 JNI 框架，请参阅 [Android 的 JNI 提示](https://developer.android.google.cn/training/articles/perf-jni?hl=zh-cn)。

Android Studio 支持适用于跨平台项目的 CMake。Android Studio 还支持 [`ndk-build`](https://developer.android.google.cn/ndk/guides/ndk-build?hl=zh-cn)，它比 CMake 速度更快，但仅支持 Android。目前不支持在同一模块中同时使用 CMake 和 `ndk-build`。

如需将现有的 `ndk-build` 库导入您的 Android Studio 项目，请了解如何[将 Gradle 关联到原生库项目](https://developer.android.google.cn/studio/projects/gradle-external-native-builds?hl=zh-cn)。

本页将向您介绍如何[设置 Android Studio](https://developer.android.google.cn/studio/projects/add-native-code?hl=zh-cn#download-ndk) 以支持必要的构建工具、如何[创建新项目](https://developer.android.google.cn/studio/projects/add-native-code?hl=zh-cn#new-project)并让其支持 C/C++，以及如何向您的项目[添加新的 C/C++ 文件](https://developer.android.google.cn/studio/projects/add-native-code?hl=zh-cn#create-sources)。

如果您想要将原生代码添加到现有项目，请按以下步骤操作：

1. 创建新的原生源代码文件，并将这些文件添加到您的 Android Studio 项目中。
   - 如果您已经拥有原生代码或想要导入预构建原生库，请跳过此步骤。
2. 配置 CMake以将原生源代码构建入库。如果您要导入并关联预构建库或平台库，则必须使用此构建脚本。
   - 如果现有的原生库已有 `CMakeLists.txt` 构建脚本，或者使用 `ndk-build` 并包含 [`Android.mk`](https://developer.android.google.cn/ndk/guides/android_mk?hl=zh-cn) 构建脚本，请跳过此步骤。
3. 提供 CMake 或 `ndk-build` 脚本文件的路径，以[配置 Gradle](https://developer.android.google.cn/studio/projects/gradle-external-native-builds?hl=zh-cn)。Gradle 使用构建脚本将源代码导入您的 Android Studio 项目并将原生库打包到应用中。

配置完项目后，使用 [JNI 框架](http://docs.oracle.com/javase/7/docs/technotes/guides/jni/spec/jniTOC.html)通过 Java 或 Kotlin 代码访问原生函数。如需构建和运行应用，请点击 **Run** 图标

> **注意**：如果现有项目使用的是已废弃的 `ndkCompile` 工具，请改为使用 CMake 或 `ndk-build`。



#### 下载 NDK 和构建工具

如需为您的应用编译和调试原生代码，您需要以下组件：

- [Android 原生开发套件 (NDK)](https://developer.android.google.cn/ndk?hl=zh-cn)：一个工具集，让您能够在 Android 应用中使用 C 和 C++ 代码。NDK 会提供平台库，让您能够管理原生 activity 和访问实体设备组件，例如传感器和触控输入。
- [CMake](https://cmake.org/)：一款外部构建工具，可与 Gradle 搭配使用来构建原生库。如果您只计划使用 `ndk-build`，则无需此组件。
- [LLDB](http://lldb.llvm.org/)：Android Studio 中用于[调试原生代码](https://developer.android.google.cn/studio/debug?hl=zh-cn)的调试程序。

如需了解如何安装这些组件，请参阅[安装及配置 NDK 和 CMake](https://developer.android.google.cn/studio/projects/install-ndk?hl=zh-cn)。

#### 创建支持 C/C++ 的新项目

创建支持原生代码的新项目的流程与[创建任何其他 Android Studio 项目](https://developer.android.google.cn/studio/projects/create-project?hl=zh-cn)的流程类似，但需要执行一个额外的步骤：

1. 在向导的 **Choose your project** 部分中，选择 **Native C++** 项目类型。
2. 点击 **Next**。
3. 填写向导下一部分中的所有其他字段。
4. 点击 **Next**。
5. 在向导的Customize C++ Support部分中，您可以使用C++ Standard字段自定义项目。
   - 使用下拉列表选择您想要使用哪种 C++ 标准化。选择 **Toolchain Default**，将使用默认的 CMake 设置。
6. 点击 **Finish**。

在 Android Studio 完成新项目的创建后，请从 Android Studio 左侧打开 **Project** 窗格，然后从菜单中选择 **Android** 视图，Android Studio 会添加 **cpp** 组。

在 **cpp** 组中，您可以找到项目中的所有原生源代码文件、头文件、CMake 或 `ndk-build` 的构建脚本，以及项目中的预构建库。对于新项目，Android Studio 会创建一个示例 C++ 源代码文件 `native-lib.cpp`，并将其置于应用模块的 `src/main/cpp/` 目录中。此示例代码提供了一个简单的 C++ 函数 `stringFromJNI()`，它会返回字符串 `"Hello from C++"`。如需了解如何向项目添加其他源代码文件，请参阅介绍如何[创建新的原生源代码文件](https://developer.android.google.cn/studio/projects/add-native-code?hl=zh-cn#create-sources)的部分。

与 `build.gradle` 文件指示 Gradle 如何构建应用一样，CMake 和 `ndk-build` 需要构建脚本才能确定如何构建您的原生库。对于新项目，Android Studio 会创建 CMake 构建脚本 `CMakeLists.txt`，并将其置于模块的根目录中。如需详细了解此构建脚本的内容，请参阅[配置 CMake](https://developer.android.google.cn/studio/projects/configure-cmake?hl=zh-cn)。



## 编写代码













## 运行和调试

### 启用调试功能

您需要先执行以下操作，然后才能开始调试：

**在设备上启用调试功能**。

如果您使用的是模拟器，则默认情况下会启用调试功能。但是，对于已连接的设备，您需要[在设备开发者选项中启用调试功能](https://developer.android.google.cn/studio/debug/dev-options?hl=zh-cn)。

**运行可调试的 build 变体**。

使用 build 配置中包含 [`debuggable true`](https://developer.android.google.cn/studio/build/build-variants?hl=zh-cn#build-types)（Kotlin 脚本中 `isDebuggable = true`）的[build 变体](https://developer.android.google.cn/studio/build/build-variants?hl=zh-cn)。通常，您可以选择每个 Android Studio 项目中都包含的默认“debug”变体（即使它在 `build.gradle` 文件中不可见）。不过，如果您想将新 build 类型定义为可调试，则必须将 `debuggable true` 添加到该 build 类型中：

```groovy
android {
    buildTypes {
        customDebugType {
            debuggable true
            ...
        }
    }
}
```

此属性也适用于[包含 C/C++ 代码的模块](https://developer.android.google.cn/studio/projects/add-native-code?hl=zh-cn)。

**注意**：`jniDebuggable` 属性已废弃。

如果您的应用依赖于您也想调试的某个库模块，则该库也必须使用 `debuggable true` 进行打包，以便保留其调试符号。为了确保应用项目的可调试变体接收库模块的可调试变体，请发布库的非默认版本。





> **注意：**Android Studio 调试程序与垃圾回收器采用松散集成。Android 虚拟机可保证在调试程序断开连接之前不会对调试程序发现的任何对象进行垃圾回收。这可能会导致在调试程序处于连接状态时，出现对象堆积情况。例如，如果调试程序发现了某个正在运行的线程，那么在调试程序断开连接之前，不会对关联的 `Thread` 对象进行垃圾回收，即使该线程已终止。



### 写入和查看日志

#### 如何读取日志

每个日志都有一个相关日期、时间戳、进程和线程 ID、标记、软件包名称、优先级和消息。不同的标记具有独特的颜色，有助于识别日志类型。每个日志条目都具有一个优先级：`FATAL`、`ERROR`、`WARNING`、`INFO`、`DEBUG` 或 `VERBOSE`。

例如，以下日志消息的优先级为 `DEBUG`，标记为 `ProfileInstaller`：

```
2022-12-29 04:00:18.823 30249-30321 ProfileInstaller        com.google.samples.apps.sunflower    D  Installing profile for com.google.samples.apps.sunflower
```

#### 配置日志视图

标准日志视图会显示每个日志的相关日期、时间进程和线程 ID、标记、软件包名称、优先级以及消息。默认情况下，消息行在日志视图中不会换行，但您可以使用 Logcat 工具栏中的 **Soft-Wrap**选项进行换行。

您可以点击 **Logcat** 工具栏中的 **Configure Logcat Formatting Options**，切换到 **Compact** 视图，其中包含更少的默认显示信息。

如需进一步配置您希望显示的信息量，请选择 **Modify Views**，然后选择是否显示时间戳、标记、进程 ID 或软件包名称。

#### 其他配置选项

如需了解其他配置选项，请依次前往 **Android Studio** > **Settings** > **Tools** > **Logcat**。在这里，您可以选择 Logcat 周期缓冲区大小、新 Logcat 窗口的默认过滤器，以及是否要添加历史记录中的过滤器以自动补全。

#### 查看查询历史记录

您可以通过点击查询字段旁边的 **Show history**来查看查询历史记录。如需收藏某个查询，使其在所有 Studio 项目中始终位于列表顶部，请点击该查询旁边的星号。您还可以使用 `name:` 键使收藏的查询更容易识别。如需了解详情，请参阅[特殊查询](https://developer.android.google.cn/studio/debug/logcat?hl=zh-cn#special-queries)。

#### 跟踪应用崩溃和重启日志

当 Logcat 发现您的应用进程已停止并重启时，会在输出中显示一条消息，例如 `PROCESS ENDED` 和 `PROCESS STARTED`。重启 Logcat 会保留会话配置（例如标签页拆分、过滤器和视图选项），以便于您轻松继续会话。



### 分析和解决崩溃问题

#### 分析堆栈轨迹

调试应用通常需要使用堆栈轨迹。当应用因错误或异常而崩溃时，系统会生成堆栈轨迹。您还可以使用 [`Thread.dumpStack()`](https://developer.android.google.cn/reference/java/lang/Thread?hl=zh-cn#dumpStack()) 等方法输出应用代码中任意位置的堆栈轨迹。

在已连接的设备上，当应用在调试模式下运行时，Android Studio 会在 **Logcat** 视图中输出并突出显示堆栈轨迹，如图 1 所示。堆栈轨迹会显示导致抛出异常的方法调用列表，以及发生调用的文件名和行号。

点击突出显示的文件名，以打开相应文件并检查方法调用的来源。点击 **Up the stack trace** 按钮 和 **Down the stack trace** 按钮可以在 **Logcat** 窗口中显示的堆栈轨迹行之间快速移动。



### 调试布局

#### 使用布局检查器和布局验证工具调试布局

您可以使用 Android Studio 中的布局检查器来显示视图层次结构并检查每个视图的属性，进而调试应用的布局。借助布局检查器，您可以将应用布局与设计模型进行比较，显示应用的放大视图或 3D 视图，以及在运行时检查应用布局的细节。如果布局是在运行时（而不是完全在 XML 中）构建的并且布局行为出现异常，该工具会非常有用。

借助[布局验证](https://developer.android.google.cn/studio/debug/layout-inspector?hl=zh-cn#layout-validation)，您可以同时预览使用不同设备和显示配置（包括可变字体大小或用户语言）时的布局，以便轻松测试各种常见的布局问题。

若要打开布局检查器，请在已连接的设备或模拟器上[运行您的应用](https://developer.android.google.cn/studio/run?hl=zh-cn)，然后依次选择 **Tools** > **Layout Inspector**。如果您在多个设备或项目之间切换，布局检查器会自动连接到在已连接设备的前台运行的可调试进程。

#### Live Updates

**Layout Display** 会呈现应用布局在设备或模拟器上的显示效果，并显示每个视图的布局边界。您可以点击每个组件进行检查。

实时布局检查器可以在应用被部署到搭载 API 级别 29 或更高版本的设备或模拟器时，提供应用界面的完整实时数据分析。

如需启用实时布局检查器，请从 **Layout Inspector** 工具栏中选择 **Live Updates**选项。

实时布局检查器包含动态布局层次结构，可随着设备上视图的变化更新 **Component Tree** 和 **Layout Display**。



#### 选择或隔离视图

视图通常用于绘制用户可看到并与之交互的内容。**Component Tree** 会实时显示应用层次结构中的每个视图组件，这有助于您调试应用布局，因为您可以直观查看应用中的元素以及与其关联的值。

如要选择某个视图，请在 **Component Tree** 或 **Layout Display** 中点击该视图。所选视图的所有布局属性都会显示在 **Attributes** 面板中。

如果布局包含重叠的视图，您可以选择不在最前面的视图，方法是在 **Component Tree** 中点击该视图，或者[旋转布局](https://developer.android.google.cn/studio/debug/layout-inspector?hl=zh-cn#rotate-layout)。

如要处理复杂的布局，您可以隔离各个视图，以便只有部分布局显示在 **Component Tree** 中并在 **Layout Display** 中呈现。

如要隔离某个视图，请在 **Component Tree** 中右键点击该视图，然后选择 **Show Only Subtree** 或 **Show Only Parents**。

如需返回完整视图，请右键点击该视图，然后选择 **Show All**。

#### 隐藏布局边框和视图标签

如需隐藏布局元素的边界框或视图标签，请点击 **Layout Display** 顶部的 **View Options** 图标，然后切换 **Show Borders** 或 **Show View Label**。

如需隐藏布局边框和视图标签，请点击 **Layout Inspector** 工具栏中的第二个 **View Options**。



### 检查网络流量

#### 使用 Network Inspector 检查网络流量

Network Inspector 会在时间轴上显示实时网络活动，包括发送和接收的数据。Network Inspector 便于您检查应用传输数据的方式和时间，并适当优化底层代码。

如需打开 Network Inspector，请按以下步骤操作：

1. 在 Android Studio 导航栏中，依次选择View >Tool Windows >App Inspection。在应用检查窗口自动连接到应用进程后，从标签页中选择Network Inspector。
   - 如果应用检查窗口未自动连接应用进程，您可能需要手动选择应用进程。
2. 从 **App Inspection** 窗口中，选择您要检查的设备和应用进程。

#### Network Inspector 概览

Network Inspector 窗口顶部显示的是事件时间轴。点击并拖动时间轴，以选择时间轴的一部分并检查该时段的网络流量。

在“详细信息”窗格中，时间图表可帮助您识别哪些地方可能发生性能问题。黄色部分的开头对应所发送请求的第一个字节。蓝色部分的开头对应所收到响应的第一个字节。蓝色部分的末尾对应所收到响应的最后一个字节。

在时间轴下方的窗格中，您可以选择以下某个标签页，以详细了解时间轴上选定时段内的网络活动：

- **Connection View**：列出了在时间轴上选定时段内从您应用的所有 CPU 线程发送或接收的文件。对于每个请求，您可以检查大小、类型、状态和传输时长。您可以通过点击任意列标题来对此列表排序。您还可看到时间轴上选定时段的明细数据，从而了解每个文件的发送或接收时间。
- **Thread View**：显示应用的每个 CPU 线程的网络活动。您可以在此视图中检查各网络请求由哪些线程负责。
- **Rules View**：Rules 有助于测试应用在遇到不同状态代码、标头和正文等响应时的行为方式。创建新规则时，为新规则命名，并在 **Origin** 子部分下添加要拦截的响应的来源信息。在 **Response** 子部分中，您可以指定修改响应的位置和方式。例如，您可以将规则设置为对具有特定状态代码的响应执行规则并修改相应状态代码。在 **Header rules** 和 **Body rules** 子部分，您可以创建子规则，用于添加或修改响应标头或正文。系统会按照表中列出的顺序应用规则。您可以通过勾选各条规则旁边的 **Active** 复选框来选择要启用或停用的规则。

从 **Connection View** 或 **Thread View** 中点击请求名称，可检查有关已发送或已接收数据的详细信息。点击各个标签页可查看响应标头和正文、请求标头和正文或调用堆栈。

在 **Response** 和 **Request** 标签页中，点击 **View Parsed** 链接可显示格式化文本，点击 **View Source** 链接可显示原始文本。





### 调试数据库

#### 使用 Database Inspector 调试数据库





### 调试WorkerManager工作器