## Android Material Design 风格之 CoordinatorLayout | 协调布局 介绍及使用详情

extends ViewGroup

implements NestedScrollingParent

> CoordinatorLayout is a super-powered FrameLayout

> CoordinatorLayout is intended for two primary use cases:

> 1.As a top-level application decor or chrome layout

> 2.As a container for a specific interaction with one or more child views

> Google官方对这个控件的大概意思这是一个加强型的帧布局，主要有两个两个用例方面：

> 1.作为顶层布局

> 2.调度协调子布局
 
那么到底CoordinatorLayout有什么功能呢！说得再好不如来张图

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/coordinator.gif)
 
### 使用方法
- CoordinatorLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:24.2.0‘
}
```

####  首先，XML中添加布局文件

```java

    <android.support.design.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

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

#### 仅仅这些布局文件，就可以实现当RecyclerView向上滑动时Toolbar隐藏，向下滑动时Toolbar显示，下面来介绍下其属性

CoordinatorLayout一般直接作为根布局使用，否则会有一些功能失效

CoordinatorLayout的使用核心是Behavior，Behavior就是执行你定制的动作。在讲Behavior之前必须先理解两个概念：Child和Dependency，什么意思呢？Child当然是子View的意思了，是谁的子View呢，当然是CoordinatorLayout的子View；其实Child是指要执行动作的CoordinatorLayout的子View。而Dependency是指Child依赖的View。比如上面的gif图中，RecyclerView就是Child，Toolbar就是Dependency，因为Toolbar的动作是依赖于RecyclerView。简而言之，就是如果RecyclerView发生了变化，Toolbar就要相应发生变化。发生变化是具体发生什么变化呢？这里就要引入Behavior，Child发生变化的具体执行的代码都是放在Behavior这个类里面，一般用系统定义好的Behavor就可以(也可以自定义)，这里我们在RecyclerView里面添加

```java
        app:layout_behavior="@string/appbar_scrolling_view_behavior"
```

当CoordinatorLayout发现RecyclerView中定义了这个属性，它会搜索自己所包含的其他view，看看是否有view与这个behavior相关联。AppBarLayout.ScrollingViewBehavior描述了RecyclerView与AppBarLayout之间的依赖关系。RecyclerView的任意滚动事件都将触发AppBarLayout或者AppBarLayout里面view的改变。AppBarLayout里面定义的view只要设置了app:layout_scrollFlags属性，就可以在RecyclerView滚动事件发生的时候被触发
app:layout_scrollFlags属性里面必须至少启用scroll这个flag，这样这个view才会滚动出屏幕，否则它将一直固定在顶部。可以使用的其他flag有：

- snap:当滑动停止时但未达到展开或者收缩的状态，这个属性会判断距离谁最近，自动展开或者收缩（加上这个属性效果比较顺畅）

- enterAlways: 一旦向上滚动这个view就可见

- enterAlwaysCollapsed: 顾名思义，这个flag定义的是何时进入（已经消失之后何时再次显示）。假设你定义了一个最小高度（minHeight）同时enterAlways也定义了，那么view将在到达这个最小高度的时候开始显示，并且从这个时候开始慢慢展开，当滚动到顶部的时候展开完。

- exitUntilCollapsed: 同样顾名思义，这个flag时定义何时退出，当你定义了一个minHeight，这个view将在滚动到达这个最小高度的时候消失。

更多方法请移步至[官方文档](https://developer.android.com/reference/android/support/design/widget/CoordinatorLayout.html)

### 自定义Behavior

为了更好的理解，我将系统自带的Behavior通过自定义来实现，同时再给FloatActionBuuton添加一个自定义的Behavior（没有添加显示和隐藏的动画效果，看起来不是很美观，但是重点不在这，对吧！嘿嘿），先看效果图

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/coordinatorbehavior.gif)

#### 1.首先，我们定义一个类，继承CoordinatorLayout.Behavior<T>,其中，泛型参数T是我们要执行动作的View类，也就是Child

- AppbarLayout的Behavior

```java

    public class AppBehavior extends CoordinatorLayout.Behavior<AppBarLayout> {

    private int directionChange;

    public AppBehavior() {
        super();
    }

    public AppBehavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    //判断滑动的方向 返回垂直滑动
    @Override
    public boolean onStartNestedScroll(CoordinatorLayout coordinatorLayout, AppBarLayout child, View directTargetChild, View target, int nestedScrollAxes) {
        return (nestedScrollAxes & ViewCompat.SCROLL_AXIS_VERTICAL) != 0;
    }

    //根据滑动的距离显示和隐藏 child
    @Override
    public void onNestedPreScroll(CoordinatorLayout coordinatorLayout, AppBarLayout child, View target, int dx, int dy, int[] consumed) {
        if (dy > 0 && directionChange < 0 || dy < 0 && directionChange > 0) {
            directionChange = 0;
        }
        directionChange += dy;
        if (directionChange > child.getHeight() && child.getVisibility() == View.VISIBLE) {
            child.setVisibility(View.GONE);
        } else if (directionChange < 0 && child.getVisibility() == View.GONE) {
            child.setVisibility(View.VISIBLE);
        }
    }
}
    
```
- Fab的Behavior和AppbarLayout的Behavior一样，因为泛型中的类型不同，所以还得再写一遍

```java

public class FabBehavior extends CoordinatorLayout.Behavior<FloatingActionButton> {

    private int directionChange;

    public FabBehavior() {
        super();
    }

    public FabBehavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    //判断滑动的方向 返回垂直滑动
    @Override
    public boolean onStartNestedScroll(CoordinatorLayout coordinatorLayout, FloatingActionButton child, View directTargetChild, View target, int nestedScrollAxes) {
        return (nestedScrollAxes & ViewCompat.SCROLL_AXIS_VERTICAL) != 0;
    }

    //根据滑动的距离显示和隐藏 child
    @Override
    public void onNestedPreScroll(CoordinatorLayout coordinatorLayout, FloatingActionButton child, View target, int dx, int dy, int[] consumed) {
        if (dy > 0 && directionChange < 0 || dy < 0 && directionChange > 0) {
            directionChange = 0;
        }
        directionChange += dy;
        if (directionChange > child.getHeight() && child.getVisibility() == View.VISIBLE) {
            child.setVisibility(View.GONE);
        } else if (directionChange < 0 && child.getVisibility() == View.GONE) {
            child.setVisibility(View.VISIBLE);
        }
    }
}

```
#### 2.在XML中给dependency添加对应的Behavior

```java

   <android.support.design.widget.CoordinatorLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.design.widget.AppBarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_behavior="com.coder.guoy.recyclerview.ui.coordinator.AppBehavior"><!--自定义的Behavior-->

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@color/colorTheme"
                android:contentInsetStart="0dp">

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
            android:layout_height="match_parent"/>

        <android.support.design.widget.FloatingActionButton
            android:id="@+id/fab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="bottom|right"
            android:layout_marginBottom="40dp"
            android:layout_marginRight="40dp"
            android:backgroundTint="@color/red"
            android:src="@drawable/back"
            app:borderWidth="0dp"
            app:elevation="10dp"
            app:fabSize="auto"
            app:layout_behavior="com.coder.guoy.recyclerview.ui.coordinator.FabBehavior"<!--自定义的Behavior-->
            app:pressedTranslationZ="20dp"
            app:rippleColor="@color/colorWrite"
            />

    </android.support.design.widget.CoordinatorLayout>

```

- OK，这就完成了自定义Behavior对AppbarLayout和Fab进行控制，不需要那些繁琐的监听和Touch事件，这里主要讲怎样自定义Behavior，并没有对UI效果进行友善的处理，如果对你有帮助，Do it by yourself!

- 注意:自定义的Behavior添加需要填写绝对路径（包名+类名）

#### 总结

我们要明确三个概念及他们之间的关系:

- Behavior(行为)，child(子View:)和dependency(从属者：根据子view的改变执行相应的行为)

- 从属者添加定义好的行为，当子view发生变化时，从属者按照定义好的行为去发生变化，

### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
