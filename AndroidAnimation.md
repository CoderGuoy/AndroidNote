# 一篇文章搞定 Android动画

一个好的动画效果可以让界面更友善的展示给用户，特别是在提示、切换和引导等场景中，合理的使用动画可以让用户获得良好的体验，下面就介绍下我们经常会用到的动画以及如何选择合理的选择动画并使用，他们有哪些区别。

## 动画分类及简介

[Drawable Animation|逐帧动画|帧动画](https://github.com/CoderGuoy/Android-Material-Design/blob/master/AndroidAnimation.md#drawable-animation逐帧动画帧动画)

帧动画这个比较好理解，就是一张张图片顺序播放，和动画片原理一样，主要应用在加载和适合循环播放的情况下使用。

[View Animation|视图动画|补间动画](https://github.com/CoderGuoy/Android-Material-Design/blob/master/AndroidAnimation.md#view-animation视图动画补间动画)

视图动画提供了四种动画方式：AlphaAnimation(透明图),RotateAnimation(旋转),TranslateAnimation(位移),ScaleAinmation(缩放),还有一个AnimationSet(动画集合:混合使用多种动画)
优点:效率高，方便使用
缺点:不具备交互性，当某个元素放缓是呢过视图动画后，其响应时间的位置还在动画前的地方，所以试图动画只能做普通的动画效果，应避免交互的发生。

[Property Animation|属性动画](https://github.com/CoderGuoy/Android-Material-Design/blob/master/AndroidAnimation.md#property-animation属性动画)

属性动画是为了改变视图动画只显示并不能响应事件在Android3.0之后推出的，Animator框架中使用组多的是AnimatorSet和ObjectAnimator的配合，ObjectAnimator可以只控制一个对象的一个属性值，用多个ObjectAnimator组合到AnimatorSet形成一个动画;而且ObjectAnimator能够自动驱动，可以调用setFrameDelay()设置动画帧之间的间隙时间，调整帧率，减少动画过程中频繁绘制界面，而在不影响动画效果的前提下减少CPU资源消耗。更重要的是，属性动画通过调整get、set方法来真实地控制一个View的属性值，因此强大的属性动画框架，基本可以实现所有的动画效果。


## Drawable Animation|逐帧动画|帧动画

### 1.先看效果

![图1](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/drawableanimation.gif)

### 2.代码实现

#### 先在res/drawable下建一个XML文件

```java
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
                android:oneshot="false">
    <item
        android:drawable="@drawable/loading_kunfu01"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu02"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu03"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu04"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu05"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu06"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu07"
        android:duration="200"/>
    <item
        android:drawable="@drawable/loading_kunfu01"
        android:duration="200"/>
</animation-list>
```

#### 在布局文件中添加使用

```java
        <ImageView
            android:id="@+id/drawable_animation"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:background="@android:color/white"
            android:scaleType="center"
            android:layout_centerInParent="true"
            android:src="@drawable/kungfu_loading"/>
```

#### 在Activity或Fragment中启动

```java

public class DrawableAnimation extends MvvmBaseActivity<ActivityDrawableAnimationBinding> {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_drawable_animation);
        showContentView();
    }

    @Override
    public void onWindowFocusChanged(boolean hasFocus) {
        super.onWindowFocusChanged(hasFocus);
        AnimationDrawable animationDrawable = (AnimationDrawable) bindingView.drawableAnimation.getDrawable();
        animationDrawable.start();
    }
}

```

### 3.方法介绍及注意事项

#### 方法介绍：

animation-list 必须要作为根元素，可以包含一或多个 item
android:onshot如果定义为true的话，此动画只会执行一次，如果为false则一直循环。
item 元素代表一帧动画
android:drawable指定此帧动画所对应的图片资源
android:druation代表此帧持续的时间，整数，单位为毫秒

#### 注意事项：

避免在onCreate中调用start()方法，因为此时窗口Window对象还没有完全初始化，AnimationDrawable不能完全追加到窗口Window对象中，那么该怎么办呢？我们需要把这段代码放在onWindowFocusChanged方法中，当Activity展示给用户时，onWindowFocusChanged方法就会被调用，我们正是在这个时候实现我们的动画效果。当然，onWindowFocusChanged是在onCreate之后被调用的。

## View Animation|视图动画|补间动画

### 1.方法介绍

#### 透明度|alpha

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/alpha.gif)

XML中

```java
     <alpha
        android:fromAlpha="起始时透明度|取0.0-1.0之间的float型"
        android:toAlpha="结束时透明度|取0.0-1.0之间的float型"
        android:duration="动画持续时间|以毫秒为单位"/>
```

在代码中

```java
AlphaAnimation alpha = new AlphaAnimation(1.0f, 0.0f);
```

0.0表示完全透明;1.0表示完全不透明

#### 旋转|rotate

XML中

```java
    <rotate       
        android:fromDegrees="起始角度"
        android:toDegrees="旋转角度"
        android:pivotX="View的X坐标的开始位置"
        android:pivotY="View的Y坐标的开始位置"
        android:duration="动画持续时间|以毫秒为单位"/>
```

在代码中

```java
RotateAnimation rotate = new RotateAnimation(0, 360,
                        RotateAnimation.RELATIVE_TO_SELF, 0.5f,
                        RotateAnimation.RELATIVE_TO_SELF, 0.5f);
```
pivotXType为动画在X轴相对于物件位置类型  
pivotXValue为动画相对于物件的X坐标的开始位置
pivotXType为动画在Y轴相对于物件位置类型   
pivotYValue为动画相对于物件的Y坐标的开始位置

当起始角度小于旋转角度时，顺时针旋转;反之则逆时针旋转。

#### 位移|translate

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/translate.gif)

XML中

```java
    <translate
        android:fromXDelta="起始时 X坐标上的位置"
        android:fromYDelta="起始时 Y坐标上的位置"
        android:toXDelta="结束时 X坐标上的位置"
        android:toYDelta="结束时 Y坐标上的位置"
        android:duration="动画持续时间|以毫秒为单位"/>
```

在代码中

```java
TranslateAnimation translate = new TranslateAnimation(
                        TranslateAnimation.RELATIVE_TO_SELF, -200,
                        TranslateAnimation.RELATIVE_TO_SELF, 0);
```

注意:没有指定fromXType toXType fromYType toYType 时候，默认是以自己为相对参照物

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/translate.png)

#### 缩放|scale

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/scale.gif)

XML中

```java
    <scale
        android:fromXScale="起始时 X坐标上的伸缩尺寸"
        android:fromYScale="起始时Y坐标上的伸缩尺寸"
        android:toXScale="结束时 X坐标上的伸缩尺寸"
        android:toYScale="结束时 Y坐标上的伸缩尺寸"
        android:pivotX="View的X坐标的开始位置"
        android:pivotY="View的Y坐标的开始位置"
        android:duration="动画持续时间|以毫秒为单位"/>
```

在代码中

```java
ScaleAnimation scale = new ScaleAnimation(0.0f, 1.0f, 0.0f, 1.0f,
                        Animation.RELATIVE_TO_SELF, 0.5f, Animation.RELATIVE_TO_SELF, 0.5f);
```
pivotXType为动画在X轴相对于物件位置类型  
pivotXValue为动画相对于物件的X坐标的开始位置
pivotXType为动画在Y轴相对于物件位置类型   
pivotYValue为动画相对于物件的Y坐标的开始位置

伸缩尺寸四种属性值:0.0表示收缩到没有;1.0表示正常无伸缩;值小于1.0表示收缩;值大于1.0表示放大

#### 组合|set

在代码中

```java
AnimationSet set = new AnimationSet(true);
set.addAnimation(alpha);
set.addAnimation(rotate);
set.addAnimation(translate);
set.addAnimation(scale);
View.startAnimation(set);
```

#### 使用XML中的动画效果
使用AnimationUtils类的静态方法loadAnimation()来加载XML中的动画XML文件
myAnimation= AnimationUtils.loadAnimation(this,R.anim.my_anim);

- 总结:对于选择用代码实现还是在XML中来实现，看这个动画是否会被多次重复使用，如果是的话优先选择XML来实现，提高代码的复用性。

### 2.插值器|interpolator 

不设置插值器默认是匀速运动

AccelerateInterpolator 加速，开始时慢中间加速

DecelerateInterpolator 减速，开始时快然后减速

AccelerateDecelerateInterolator 先加速后减速，开始结束时慢，中间加速

AnticipateInterpolator 反向，先向相反方向改变一段再加速播放

AnticipateOvershootInterpolator 反向加超越，先向相反方向改变，再加速播放，会超出目的值然后缓慢移动至目的值

BounceInterpolator 跳跃，快到目的值时值会跳跃，如目的值100，后面的值可能依次为85，77，70，80，90，100

CycleIinterpolator 循环，动画循环一定次数，值的改变为一正弦函数：Math.sin(2* mCycles* Math.PI* input)

LinearInterpolator 线性，线性均匀改变

OvershottInterpolator 超越，最后超出目的值然后缓慢改变到目的值


## Property Animation|属性动画

### 1.先看效果

### 2.代码实现

### 3.方法介绍


[API](https://developer.android.com/reference/android/view/animation/Animation.html)

