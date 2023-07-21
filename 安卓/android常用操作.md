### 快捷操作

自动补全代码	`alt+/`

使用系统asset资源	`new>Vector Asset`

函数快捷键	`alt+insert`

快速复制	`ctrl+d`

复制多行	按中`alt`

打开类 	`ctrl+N`



### Git使用

[Android Studio配置GitHub同步代码步骤](https://blog.csdn.net/qq_46128318/article/details/111544246)



### 控件使用

#### 使用RecyclerView

+ setLayoutManager(new LinearLayoutManager(this))

+ 此外，RecyclerView比ListView的设置要复杂一些，主要是它需要自己去自定义分割线，设置动画和布局管理器，等等。布局文件activity_recycler_view.xml如下：

+ Adaper最大的改进就是对ViewHolder进行了封装定义，我们只需要**自定义一个ViewHolder**继承RecyclerView.ViewHolder 就可以了。另外，Adaper 继承了 RecyclerView.Adapter，在onCreateViewHolder中**加载条目布局**，在onBindViewHolder中**将视图与数据进行绑定**。

[Android中关于notifyDataSetChanged()方法的注意](https://blog.csdn.net/weixin_38420342/article/details/105291739)

[Android开发——RecyclerView特性以及基本使用方法（一）](https://blog.csdn.net/SEU_Calvin/article/details/53872276)

[RecyclerView基本原理](https://www.likecs.com/show-203999723.html)







### 注意事项

相同文件下如果控件id相同是不允许的，eclipse会报错的，如果不是同在一个布局文件中的话就可以，findviewbyid ()找的那个id是你前面用setContentView(R.layout.*)中的xml文件中的id

