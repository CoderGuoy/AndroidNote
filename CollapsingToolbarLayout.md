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

#### Parallax scrolling children | 子View视差滚动
Child views can opt to be scrolled within this layout in a parallax fashion. See COLLAPSE_MODE_PARALLAX and setParallaxMultiplier(float).

子view可以选择在布局里滚动 见COLLAPSE_MODE_PARALLAX 和 setParallaxMultiplier(float)

#### Pinned position children | 固定子view位置
Child views can opt to be pinned in space globally. This is useful when implementing a collapsing as it allows the Toolbar to be fixed in place even though this layout is moving. 
See COLLAPSE_MODE_PIN.

子视图可以选择在全球范围内的空间被固定,这是一个有用的实现当Toolbar被固定在适当的位置甚至移动,见 COLLAPSE_MODE_PIN

Do not manually add views to the Toolbar at run time. We will add a 'dummy view' to the Toolbar which allows us to work out the available space for the title. 
This can interfere with any views which you add.

注意：不要手动在运行时添加视图工具栏.我们将添加一个"虚拟视图"在Toolbar,使我们可以能够制定出标题空间.你可以添加认识View在这个接口

先看效果图

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/Coordinatorlayout00.gif)

### 使用方法
CollapsingToolbarLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:22.2.0+‘
}
```

####  XML中添加布局文件
```java
 <android.support.design.widget.CoordinatorLayout
        android:id="@+id/coordinatorlayout"
        android:layout_width="match_parent"
        android:fitsSystemWindows="true"
        android:layout_height="match_parent">

        <android.support.design.widget.AppBarLayout
            android:id="@+id/appbarlayout"
            android:layout_width="match_parent"
            android:fitsSystemWindows="true"
            android:layout_height="wrap_content">

            <android.support.design.widget.CollapsingToolbarLayout
                android:id="@+id/collapsingtoolbar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:fitsSystemWindows="true"
                app:contentScrim="@color/colorTheme"
                app:layout_scrollFlags="scroll|exitUntilCollapsed|snap"
                app:title="@string/CoordinatorLayout">

                <ImageView
                    android:layout_width="match_parent"
                    android:layout_height="200dp"
                    android:scaleType="centerCrop"
                    android:src="@drawable/model02"
                    app:layout_collapseMode="parallax"/>

                <android.support.v7.widget.Toolbar
                    android:layout_width="match_parent"
                    android:layout_height="?android:attr/actionBarSize"
                    android:contentInsetStart="0dp"
                    app:layout_collapseMode="pin">

                    <ImageView
                        android:id="@+id/imageview_back"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:paddingBottom="20dp"
                        android:paddingLeft="5dp"
                        android:paddingRight="20dp"
                        android:paddingTop="20dp"
                        android:src="@drawable/back"/>

                </android.support.v7.widget.Toolbar>
            </android.support.design.widget.CollapsingToolbarLayout>
        </android.support.design.widget.AppBarLayout>

        <android.support.v4.widget.NestedScrollView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_behavior="@string/appbar_scrolling_view_behavior">

            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:text="@string/text"
                android:textSize="22sp"/>

        </android.support.v4.widget.NestedScrollView>

    </android.support.design.widget.CoordinatorLayout>
```

#### 代码中添加
```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_play_coordinator_toolbar);
        showContentView();

        //设置Collapsing折叠后文字颜色
        bindingView.collapsingtoolbar.setCollapsedTitleTextColor(Color.WHITE);
        //设置Collapsing未折叠文字颜色
        bindingView.collapsingtoolbar.setExpandedTitleColor(Color.BLACK);
    }
```

OK，这就完成了如效果图上的效果，可以自己尝试一下，下面贴上一些可以自己定制需求常用的方法

collapsedTitleGravity | 设置折叠后的标题位置

contentScrim | 设置折叠后的scrim

expandedTitleGravity | 设置展开后的标题位置

scrimAnimationDuration | 设置scrim动画渐显事件(毫秒值)

title | 设置标题内容 

layout_scrollFlags | 设置折叠方式，同[CoordinatorLayout]()的scrollFlags一样

### 总结

CoolapsingToolbarLayout主要是包括Toolbar使用更多的效果，状态栏动态改变颜色使用时需要注意android版本为5.0以上和设置fitsystemwindow属性

更多方法查看[官方API](https://developer.android.com/reference/android/support/design/widget/CollapsingToolbarLayout.html)

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
