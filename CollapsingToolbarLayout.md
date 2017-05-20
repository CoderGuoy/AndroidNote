## Android 5.0新控件 CollapsingToolbarLayout | 折叠工具布局 介绍及使用详情

extends FrameLayout

先让我们来看下Google对这个组件的定义(英语好的同学请忽略我的翻译)

> CollapsingToolbarLayout is a wrapper for Toolbar which implements a collapsing app bar. It is designed to be used as a direct child of a AppBarLayout. 
CollapsingToolbarLayout contains the following features:

CollapsingToolbarLayout 是一个包裹 Toolbar 来实现折叠的app bar.它直接作为AppBarLayout的子View,包含以下功能:

#### Collapsing title | 折叠标题
> A title which is larger when the layout is fully visible but collapses and becomes smaller as the layout is scrolled off screen. You can set the title to display via setTitle(CharSequence). 
The title appearance can be tweaked via the collapsedTextAppearance and expandedTextAppearance attributes.

标题哪个更大当布局完全可见但是折叠时和布局滚动关闭屏幕变得更小，你可以通过设置标题通过 setTitle(CharSequence)
标题外观可以通过 CollapsedTitleTextAppearance 和 expandedTieleTextAppearance 来设置

#### Content scrim | 内容纱幕
A full-bleed scrim which is show or hidden when the scroll position has hit a certain threshold. You can change this via setContentScrim(Drawable).

一个显示或隐藏的纱幕当它滚动到一个指定的阈值，可以设置这个内容纱幕 setContentScrim

#### Status bar scrim | 状态栏纱幕
A scrim which is show or hidden behind the status bar when the scroll position has hit a certain threshold. You can change this via setStatusBarScrim(Drawable). 
This only works on LOLLIPOP devices when we set to fit system windows.

一个显示或隐藏的纱幕当屏幕滚动到指定阈值时的状态栏后面，你可以改变这个状态栏 setStatusBarScrim
这个仅适用于5.0以上的版本并且设置了属性fitSystemWindows 为 true的情况下

#### Parallax scrolling children
Child views can opt to be scrolled within this layout in a parallax fashion. See COLLAPSE_MODE_PARALLAX and setParallaxMultiplier(float).



#### Pinned position children
Child views can opt to be pinned in space globally. This is useful when implementing a collapsing as it allows the Toolbar to be fixed in place even though this layout is moving. 
See COLLAPSE_MODE_PIN.

Do not manually add views to the Toolbar at run time. We will add a 'dummy view' to the Toolbar which allows us to work out the available space for the title. 
This can interfere with any views which you add.


![]()
 

### 使用方法
CollapsingToolbarLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:22.2.0+‘
}
```

####  1.首先，XML中添加布局文件

##### Step1


#### 2.然后，编写相应的逻辑代码

##### Step2



- OK，这就完成了如效果图上的效果，可以自己尝试一下，下面贴上一些可以自己定制需求常用的方法



### 遇到的问题

#### Question1：

### 完整代码点我下载[GigHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
