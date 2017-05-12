## Android 5.0新控件 CardView 介绍及使用详情

> CardView:一个具有圆角的背景和阴影的FrameLayout

先上图

![]()

### 使用方法

#### 1.首先，gradle添加依赖

```java
dependencies {
    compile ‘com.android.support:cardview-v7:24.2.0‘
}
```
#### 2.XML中添加CardView
```java
        <android.support.v7.widget.CardView
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:clickable="true"
            android:foreground="?android:attr/selectableItemBackground"
            android:stateListAnimator="@drawable/lift_on_touch"
            app:cardCornerRadius="2dp"
            app:cardPreventCornerOverlap="false"
            app:cardUseCompatPadding="true"/>
```

#### 3.介绍下相关的API

cardBackgroundColor 设置CardView的背景色

cardCornerRadius 设置CardView的圆角大小(官方建议为2dp)

cardElevation 设置CardView的Z轴高度

cardMaxElevation 设置CardView的Z轴最大高度值

cardUseCompatPadding 在Android 5.0之前的系统，CardView会自动添加一些额外的padding空间来绘制阴影部分，这也导致了以5.0为分界线的不同系统上CardView的尺寸大小不同。为了解决这个问题，有两种方法：第一种，使用不同API版本的dimension资源适配（也就是借助values和values-21文件夹中不同的dimens.xml文件）；第二种，就是使用 setUseCompatPadding 属性，设置为true（默认值为false），让CardView在不同系统中使用相同的padding值

cardPreventCornerOverlap 这也是一个解决系统兼容的问题。在Android 5.0之前的系统，CardView不会裁剪内容元素以满足圆角需求，而是使用添加padding的替代方案，从而使内容元素不会覆盖CardView的圆角。而控制这个行为的属性就是cardPreventCornerOverlap，默认值为true。在本例中我们设置了该属性为false。

contentPadding 可代替普通padding等一些属性
contentPaddingLeft
contentPaddingTop
contentPaddingRight
contentPaddingBottom

#### 4.Ripple效果

Cards一般都是可点击的，为此我们使用了android:foreground属性并使用系统的?android:attr/selectableItemBackground值，同时设置clickable为true

#### 5.lift_on_touch

官网 [Material motion](https://material.google.com/motion/material-motion.html#) 部分对交互动作规范的指导，Cards、Button等视图应该有一个触摸抬起（lift-on-touch）的交互效果，也就是在三维立体空间上的Z轴发生位移，从而产生一个阴影加深的效果，与Ripple效果共同使用,如效果图所示

![]()

实现这个效果也很简单，可以在 res/drawable 目录下建立一个 lift_on_touch.xml 文件

```java
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="true" android:state_pressed="true">
        <objectAnimator
            android:duration="@android:integer/config_shortAnimTime"
            android:propertyName="translationZ"
            android:valueTo="8dp"
            android:valueType="floatType"/>
    </item>
    <item>
        <objectAnimator
            android:duration="@android:integer/config_shortAnimTime"
            android:propertyName="translationZ"
            android:valueTo="2dp"
            android:valueType="floatType"/>
    </item>
</selector>
```
即通过属性动画动态改变translationZ值，沿着Z轴，从2dp到8dp变化。这里的差值范围也是有出处的，参考 [Material motion](https://material.google.com/motion/material-motion.html#)。然后将其赋值给 android:stateListAnimator 属性即可。由于 stateListAnimator 属性只适用于Lollipop及以上版本，为了隐藏xml中的版本警告，可以指定 tools:targetApi="lollipop"

### 总结

CardView简单来说是一个带有交互效果并可以包裹组件的一个容器，可以单独使用，也可用作ListView、RecyclerView等的Item
更多的使用规范可以参考[Material Design | 材料设计风格官网](https://material.io/)(英文不好的同学可以使用谷歌浏览器，打开网页后右键点翻译成中文)

### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
