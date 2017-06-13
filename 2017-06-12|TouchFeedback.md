## Touch Feedback|Material Design动画 触摸反馈

> Touch Feedback 使用颜色和照明来响应触摸，加强手势的结果行为。每当用户触摸您应用中的可操作区域时，提供可视化响应。这可以让用户知道哪个对象被触摸，并且您的应用程序正在“听”。

先看图再说

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/touchfeedback1.gif)

### 1.可以使用系统自带的两个Ripple波纹效果

```java
//有边界
?android:attr/selectableItemBackground
//无边界 （要求API21以上）
?android:attr/selectableItemBackgroundBorderless 
```
这里的颜色是系统默认的，可以在theme里更改默认的波纹颜色
```java
 <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- API 21 theme customizations can go here. -->
        <item name="android:colorControlHighlight">#ff0000</item>
 </style>
```
注意：设置Ripple的View必须具有foucesable或clickable为true才会有效果

### 2.自定义Ripple

#### 无边界
在drawable文件夹下创建Ripple_touchborderless
```java
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="#4285f4">
</ripple>
```

#### 有边界
在drawable文件夹下创建Ripple_touch
```java
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="#4285f4"><!--波纹点击颜色-->
    <!-- 添加边界 -->
    <item android:drawable="@color/blue50"/>
</ripple>
```
注意:
1.如果ripple设置了radius(波纹大小),则触摸显示的波纹为设置的固定大小，不会为手指触摸点开始显示
2.边界的颜色如果没有透明度，为纯色的话，会遮盖其它无边界的波纹，如图

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/touchfeedback2.gif)

#### 代码如下
```java
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/image_touch1"
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:background="?android:attr/selectableItemBackground"
            android:clickable="true"
            android:gravity="center"
            android:text="波纹有边界\nselectableItemBackground"
            android:textSize="20sp"/>

        <TextView
            android:id="@+id/image_touch2"
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:layout_marginTop="5dp"
            android:background="?android:attr/selectableItemBackgroundBorderless"
            android:clickable="true"
            android:gravity="center"
            android:text="波纹超出边界\nselectableItemBackgroundBorderless"
            android:textSize="20sp"/>

        <TextView
            android:id="@+id/image_ripple"
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:layout_marginTop="5dp"
            android:background="@drawable/ripple_touch"
            android:clickable="true"
            android:gravity="center"
            android:text="自定义波纹动画|有边界"
            android:textSize="20sp"/>

        <TextView
            android:id="@+id/image_rippleborderless"
            android:layout_width="match_parent"
            android:layout_height="100dp"
            android:layout_marginTop="5dp"
            android:background="@drawable/ripple_touchborderless"
            android:clickable="true"
            android:gravity="center"
            android:text="自定义波纹动画|无边界"
            android:textSize="20sp"/>
    </LinearLayout>
```


### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)

