## Android Material Design 风格之 FloatingActionButton | 悬浮按钮 介绍及使用详情

extends ImageView

> Floating action buttons are used for a special type of promoted action. They are distinguished by a circled icon 
floating above the UI and have special motion behaviors related to morphing, launching, and the transferring anchor point.

> Google官方对这个控件的大概意思是这是一个用于特殊类型的升级动作的按钮,它们的区别在于浮动在UI上面的圆圈图标，并具有与变形，
启动和转移锚点相关的特殊运动行为。

啥意思呢,看图吧！

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/fab.gif)

谷歌官方对这个控件做了一些常用的规则

- 不是每个app 都需要FAB，如果需要的话最多也是只要一个FAB即可

- FAB的icon应该是表示一个动词，而不能是一个名词

- FAB必须得是圆形的icon 不能是其他样子。并且只能是2d平面图。不能带有3d的投影之类的效果

### 使用方法

- FloatingActionButton来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:appcompat-v7:23.4.0‘
    compile ‘com.android.support:design:24.2.0‘
}
```

#### 首先,布局文件添加，里面的属性下面有解释

##### Step1

```java

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
                app:pressedTranslationZ="20dp"
                app:rippleColor="@color/colorWrite"
                />

```

fab继承imageview，所以点击事件都是一样，这里就不叙述了

#### 到这里最基础的搭配使用就ok了，下面贴上一些可以自己定制需求常用的方法

- 在XML中

```java

fab的大小  默认:normal  迷你:mini  自适应:auto

app:fabSize           

边框宽度，通常设置为0 ，用于解决Android 5.X设备上阴影无法正常显示的问题

borderWidth

fab的背景颜色  默认颜色是theme中的colorAccent

backgroundTint        

fab按下波纹效果的颜色

rippleColor   

正常显示的阴影大小

app:elevation

点击时显示的阴影大小

app:pressedTranslationZ

```

- 在逻辑代码中

```java

隐藏fab

hide()

显示fab

show()

fab按下波纹效果的颜色

setRippleColor()

```


### 遇到的问题

#### Question1：

### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)



