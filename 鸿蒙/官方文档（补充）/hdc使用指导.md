#### 注意事项

+ hdc出现异常，使用hdc kill命令沙雕hdc服务，或者hdc start -r命令重启服务进程进行解决
+ 如果出现hdc list targets获取不到设备信息，通过任务管理器查看是否有hdc进程存在，如果进程存在，可以通过杀掉该进程进行解决。



#### option相关命令

+ **-h/help -v/version** 用于显示hdc相关的帮助、版本信息



+ **-l 0-5** 用于指定运行时日志等级，默认为LOG_INFO（3）

  `hdc -l5 start`



+ **-t key** 用于连接指定设备表示为key的设备



+ **checkserver** 用于获取client-server版本

  `hdc checkserver`



#### 查询设备列表的命令

+ **list targets [-v]** 添加-v选项，则会打印详细信息

  `hdc list targets`、`hdc list targets -v`



#### 服务进程相关命令

服务进程涉及以下命令：

+ **target mount** 以读写模式挂载系统分区。

  `hdc target mount`



+ **target boot** 设备重启

  `hdc target boot`



+ **smode [-r]** 授予后台服务进程root权限，使用-r参数取消授权

  `hdc smode`、`hdc smode -r`



+ **kill [-r]** 终止服务进程。-r触发服务重启

  `hdc kill`



+ **start [-r]** 启动服务进程。如果服务进程已经启动，-r选项触发重启
  `hdc start`



#### 网络相关命令

+ **tconn host\[:port][-remove]** 通过【ip地址：端口号】来指定连接设备。-remove表示断开连接

  `hdc tconn 192.168.0.100:8710`



+ **tmode usb** 执行后设备端对应daemon进程重启，并首先选用USB连接方式。

  `hdc tmode usb`



+ **tmode port port-number** 执行后设备端对应daemon进程重启，并优先使用网络方式连接设备，如果连接设备失败，再选择USB连接。

  `hdc tmode port 8710`

  

+ **rport remotenode localnode** 端口转发，指定设备侧端口转发数据到主机端口。

  `hdc rport tcp:2080 tcp:2345`



+ **fport ls** 列出全部转发端口转发任务。

  `hdc fport ls`



+ **fport rm** 删除指定端口转发任务。

  `hdc fport rm tcp:1234 tcp:1080`



#### 文件相关的命令

文件部分设计以下命令：

+ **file send local remote** 发送文件至远端设备

  local 本地待接收文件路径

  remote 远程待发送文件路径

  `hdc file send E:\a.txt /data/local/tmp/a.txt`



+ **file recv [-a] remote local** 从远端设备接收文件至本地。

  -a 文件保留时间戳模式

  local 本地待接收文件路径

  remote 远程待发送文件路径

  `hdc file recv /data/local/tmp/a.txt ./a.txt`



#### 应用相关的命令

应用部分涉及以下命令：

+ **install [-r/-d/-g] package** 安装OpenHarmony APP package

  -r 替换已存在应用

  -d 允许降级安装

  -g 应用动态授权

  `hdc install hwadmin.hap`



+ **uninstall [-k] package** 卸载OpenHarmony应用。

  -k 保留/data/cache

  `hdc uninstall package`



#### 调试相关的命令

调试涉及以下命令：

+ **hilog** 支持抓取log信息。

  抓取hilog日志`hdc hilog`、清理hilog缓存日志`hdc shell "hilog -r"`



+ **shell [command]** 远程执行命令或进入交互命令环境

  `hdc shell`



+ **jpid** 获取可调试进程列表。

  `hdc jpid`



