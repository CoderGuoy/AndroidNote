## Android 5.0新控件 AppBarLayout | 嵌套布局 介绍及使用详情

extends LinearLayout

> AppBarLayout is a vertical LinearLayout which implements many of the features of material designs app bar concept, namely scrolling gestures.

> 嵌套布局是一个垂直的LinearLayout，其实现了许多的材料设计应用栏概念的特征，像滚动手势

- AppBarLayout通常直接作为CoordinatorLayout的直接子View使用，否则它的大部分功能将失效。
- 它的子View通过setScrollFlags(int)或XML属性：app:layout_scrollFlags来设置滚动行为

下面是官方给出的事例

```java
 <android.support.design.widget.CoordinatorLayout
         xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:app="http://schemas.android.com/apk/res-auto"
         android:layout_width="match_parent"
         android:layout_height="match_parent">

     <android.support.v4.widget.NestedScrollView
             android:layout_width="match_parent"
             android:layout_height="match_parent"
             app:layout_behavior="@string/appbar_scrolling_view_behavior">

         <!-- Your scrolling content -->

     </android.support.v4.widget.NestedScrollView>

     <android.support.design.widget.AppBarLayout
             android:layout_height="wrap_content"
             android:layout_width="match_parent">

         <android.support.v7.widget.Toolbar
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

         <android.support.design.widget.TabLayout
                 ...
                 app:layout_scrollFlags="scroll|enterAlways"/>

     </android.support.design.widget.AppBarLayout>

 </android.support.design.widget.CoordinatorLayout>
```

我们来看下效果图

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/coordinator.gif)
 

### 使用方法
AppBarLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:22.2.0+‘
}
```

####  1.首先，XML中添加布局文件

```java
    <android.support.design.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            >

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@color/colorTheme"
                android:contentInsetStart="0dp"
                app:layout_scrollFlags="scroll|enterAlways|snap">

                <ImageView
                    android:id="@+id/imageview_back"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:paddingBottom="20dp"
                    android:paddingLeft="5dp"
                    android:paddingRight="20dp"
                    android:paddingTop="20dp"
                    android:src="@drawable/back"/>

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="CoordinatorLayout"
                    android:textColor="@android:color/white"/>

            </android.support.v7.widget.Toolbar>
        </android.support.design.widget.AppBarLayout>

        <android.support.v7.widget.RecyclerView
            android:id="@+id/recyclerview"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:layout_behavior="@string/appbar_scrolling_view_behavior"/>

    </android.support.design.widget.CoordinatorLayout>
```

app:layout_behavior="@string/appbar_scrolling_view_behavior"给滑动的布局添加滑动行为（behavior可以用系统提供的，也可自定义）
AppBarLayout的子View设置了app:layout_scrollFlags="scroll|enterAlways|snap"属性，就会随滑动而显示与隐藏

[对scrollFlags属性不熟悉的可以参考这篇文章](http://blog.csdn.net/lengxuechiwu1314/article/details/71090247)

- OK，这就完成了如效果图上的效果，这里并没有对UI进行优化，可以自己尝试一下


[官方API](https://developer.android.com/reference/android/support/design/widget/AppBarLayout.html)

### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:coderguoy@gmail.com
