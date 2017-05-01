## Android Material Design 风格之 CoordinatorLayout | 文字输入布局 介绍及使用详情

extends ViewGroup

implements NestedScrollingParent

> CoordinatorLayout is a super-powered FrameLayout

> CoordinatorLayout is intended for two primary use cases:

> 1.As a top-level application decor or chrome layout

> 2.As a container for a specific interaction with one or more child views

> Google官方对这个控件的大概意思这是一个加强型的帧布局，主要有两个两个用例方面：

> 1.作为顶层布局

> 2.调度协调子布局
 
很多文章都是把CoordinatorLayout、AppbarLayout、CollapsingToolbarLayout 以及Toolbar等一起使用介绍，
这让我不知不觉在心中认为把这几个布局要一起使用，而且只是用于特定的场景中。其实CoordinatorLayout的功能并不是局限于与AppBarLayout一起使用，
它的功能很强大的，本文主要对CoordinatorLayout的使用进行介绍，后面再写一篇文章将AppBarLayout和CollapsingToolBarLayout整合CoordinatorLayout一起。
那么到底CoordinatorLayout有多好用，请往下感受吧~

说得再好不如来张图

![]()
 
### 使用方法
- CoordinatorLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:24.2.0‘
    compile 'com.android.support:appcompat-v7:24.2.0'
}
```

####  首先，XML中添加布局文件

##### Step1

```java

```

#### 然后，编写相应的逻辑代码

##### Step2

```java

```

- 下面贴上一些可以自己定制需求常用的方法

```java

```

### 遇到的问题

#### Question1：



### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
