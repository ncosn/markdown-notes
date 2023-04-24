# github

### 常用 Linux 命令

##### ls 命令

```shell
$ ls					# 查看当前目录
```

##### cd 命令

```shell
$ cd markdown/github	# 打开目录
$ cd ..					# 返回上一级目录
```

##### pwd 命令

```shell
$ pwd					# 查看当前路径
```

##### clear 命令

```shell
$ clear					# 清屏
```

##### history 命令

```shell
$ history				# 查看历史命令
```

##### touch 命令

```shell
$ touch index.js		# 新建文件
```

##### mkdir 命令

```shell
$ mkdir test			# 新建文件夹
```

##### rm 命令

```shell
$ rm index.js			# 删除文件
$ rm -f test			# 删除文件夹
```

##### mv 命令

```shell
$ mv index.js test		# 移动文件
```

##### help 命令

```shell
$ help					# 查看帮助
```



### git 环境配置

```shell
$ git config --system --list	# 查看系统配置
$ git config --global --list	# 查看用户配置

$ git config --global user.name "tacit"					# 名称
$ git config --global user.email "1270854970@qq.com"	# 邮箱
```



### git 工作原理

Workspace：工作区，平时存放项目代码的地方

Index / Stage：暂存区，用于临时存放你的改动，事实上只是一个文件，保存即将提交到文件列表信息

Repository：仓库区（本地仓库），就是安全存放数据的位置。其中HEAD指向最新放入仓库的版本

Remote：远程仓库，托管代码的服务器。

<img src="C:\Users\86181\AppData\Roaming\Typora\typora-user-images\image-20220226143504080.png" alt="image-20220226143504080" style="zoom:67%;" />

<img src="C:\Users\86181\AppData\Roaming\Typora\typora-user-images\image-20220226144010074.png" alt="image-20220226144010074" style="zoom:50%;" />

### 配置 SSH 公钥

……

### IDEA 中集成 Git 操作

……

### 常用Git 命令

#### 学习

[Git 大全](https://gitee.com/all-about-git)、[Git 命令学习](https://learngitbranching.js.org/?locale=zh_CN)、[Git 教程 - 廖雪峰](https://www.liaoxuefeng.com/wiki/896043488029600)

#### 主要

##### 1.git commit

```shell
$ git commit -m [message]					# 提交
```

##### 2.git checkout

```shell
$ git checkout [branch-name]				# 切换到指定分支，并更新工作区
$ git checkout -b [branch]					# 新建一个分支，并切换到该分支
```

> HEAD 是一个对当前检出记录的符号引用，总是指向当前分支上最近一次提交记录，通常指向分支名。

##### 3.git branch

```shell
$ git branch								# 列出所有本地分支
$ git branch -r								# 列出所有远程分支
$ git branch [branch-name]					# 新建一个分支，但停留在当前分支
$ git checkout -b [branch-name]				# 新建一个分支，并切换到该分支
$ git branch -d [branch-name]				# 删除分支
$ git push origin --delete [branch-name]	# 删除远程分支
$ git branch -f [eg.main] [eg.HEAD~3]		# 强制修改分支位置
```

##### 4.git merge

```shell
$ git merge [branch-name]					# 合并指定分支到当前分支
```

##### 5.git rebase

```shell
$ git rebase [base]							# 变基，丢失合并提交的上下文，产生线性项目历史记录
$ git rebase [base] [B]						# base为基，将B合并过去
$ git rebase -i [eg.HEAD~4]					# 带参数--interactive，交互式rebase
$ git rebase --onto [branch] [commit_id1] [commit_id2]	#切片id1~id2左开右闭添加到branch分支
```

> **git merge vs git rebase**
>
> git merge:
>
> + 记录合并动作，很多时候这些是垃圾信息
> + 不修改原 `commit ID`
> + 只解决一次冲突
> + 分支不整洁
>
> git rebase:
>
> + 改变当前分支 `branch out` 的位置
> + 简洁的项目历史
> + 每个 `commit` 都需要解决冲突
> + 修改所有 `commit ID`

##### 6.相对引用

```shell
$ git checkout [eg.main]^					# 向上移动一个提交记录
$ git checkout [eg.HEAD]~4					# 向上移动四个提交记录
$ git branch [branch-name] HEAD^2			# 选择当前分支第二个父节点添加分支
```

##### 7.撤销变更

```shell
$ git reset [commit eg.HEAD~1]				# 回退提交记录来撤销改动（只对本地有效）
$ git revert [commit eg.HEAD]				# 增加新提交来撤销更改
```

##### 8.git cherry-pick

```shell
$ git cherry-pick [commit]					# 选择一个或多个commit，合并进当前分支，类似rebase
```

##### 9.git tag

```shell
$ git tag [tag] [commit]					# 建立一个标签指向提交记录（省略commit则用HEAD）
```

##### 10.git describe

```shell
$ git describe <ref>	#<ref>可以是任何能被Git识别成提交记录的引用（省略则为HEAD），输出结果为<tag>_<numCommits>_g<hash>，tag表示离ref最近的标签，numCommits是表示这个ref与tag相差多少个提交记录，hash表示的是你所给定ref所表示的提交记录的前几位。当ref提交记录上有某个标签时，则只输出标签名称。
```

#### 远程

##### 1.git clone

```shell
$ gitr clone [url]							# 下载一个项目和它的整个代码历史
```

##### 2.git fetch

```shell
$ git fetch [remote]						# 下载远程仓库的所有变动，更新远程分支指针，远程同步
```

##### 3.git pull

```shell
$ git pull [remote] [branch]				# 取回远程仓库的变化，并与本地分支合并（git fetch + git merge）
$ git pull --rebase							# 取回远程仓库的变化，并与本地分支合并（git fetch + git rebase）
```

##### 4.git push

```shell
$ git push [remote] [branch]				# 上传本地指定分支到远程仓库
$ git push [remote] <source>:<destination>	#
```

#####  5.远程跟踪分支

```shell
$ git checkout -b [branch] [origin/main]	# 新建分支跟踪远程分支
$ git branch -u [origin/main] [branch]		# 设置远程跟踪分支
```

#### 使用

##### 1.创建版本库

+ 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件；

+ 使用命令`git commit -m <message>`，完成。

##### 2.时光机穿梭

+ 要随时掌握工作区的状态，使用`git status`命令；
+ 如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。

1）版本回退

+ `HEAD`指向的版本就是当前版本，因此，Git 允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`；
+ 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本；
+ 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

2）管理修改

+ 第一次修改 -> `git add` -> 第二次修改 -> `git add` -> `git commit`；

+ Git是如何跟踪修改的，每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。

3）撤销修改

+ 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
+ 场景2：当你改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
+ 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考”版本回退”一节，前提是没有推送到远程库。

4）删除文件

+ 要从版本库中删除该文件，那就用命令`git rm <file>`删掉，并且`git commit`。

##### 3.远程仓库

1）添加远程库

+ 要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；
+ 关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名；
+ 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；
+ 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。

2）删除远程库

+ 想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息；
+ 此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

3）从远程库克隆

+ 要克隆一个仓库，首先要知道仓库的地址，然后使用`git clone`命令克隆；
+ Git支持多种协议，包括`https`，但`ssh`协议速度最快。



**总结**：其实只需要进行下面几步就能把本地项目上传到Github

1、在本地创建一个版本库（即文件夹），通过git init把它变成Git仓库；

2、把项目复制到这个文件夹里面，再通过git add .把项目添加到仓库；

3、再通过git commit -m “注释内容”把项目提交到仓库；

4、在Github上设置好SSH密钥后，新建一个远程仓库，通过git remote add origin https://github.com/*****/test1.git将本地仓库和远程仓库进行关联；

5、最后通过git push -u origin master(没有README文件，仓库为空)把本地仓库的项目推送到远程仓库（也就是Github）上；（若新建远程仓库的时候自动创建了README文件或以前就有文件使用：$ git pull --rebase origin master再用$ git push origin master推送）。
