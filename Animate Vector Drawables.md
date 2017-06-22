## Material Design动画 Animated Vector Drawables|矢量图片动画

> [AnimatedVectorDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedVectorDrawable.html)类可以去创建一个矢量资源的动画

先看效果图

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/animatedvectordrawable_smile.gif)![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/animatedvectordrawable_heart_bonc.gif)

> Animated Vector Drawables实现动画主要分三个步骤：<br/>
- 1.画出SVG图片 
- 2.画出动画变化路径/条件 
- 3.通过animated-vector将图片和动画连接起来

### 实现步骤(以实现悲伤变笑脸为例)

####  1.在res/drawable/下创建vector元素的矢量资源文件

vector_smile.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
        android:width="200dp"
        android:height="200dp"
        android:viewportHeight="100"
        android:viewportWidth="100">
    <path
        android:fillColor="@color/yello"
        android:pathData="M 50,50 m -48 ,0 a 48,48 0 1,0 96,0 a 48,48 0 1,0 -96,0"/>
    <path
        android:name="eye_left"
        android:pathData="M 28,40 Q 36,50 43,40"
        android:strokeColor="@color/colorBlack"
        android:strokeLineCap="round"
        android:strokeWidth="4"/>
    <path
        android:name="eye_right"
        android:pathData="M 58,40 Q 66,50 73,40"
        android:strokeColor="@color/colorBlack"
        android:strokeLineCap="round"
        android:strokeWidth="4"/>
    <path
        android:name="smile"
        android:pathData="M 30,75 Q 50,55 70,75"
        android:strokeColor="@color/colorBlack"
        android:strokeLineCap="round"
        android:strokeWidth="4"/>
</vector>
```
OK,一个笑脸的SVG图片就画好了，如果还不熟悉如何画SVG图片，请看这篇博客学习[]()

####  2.在res/animator/下为眼睛和嘴创建动画变化路径

我们需要为每个变化的部分添加动画：

##### 首先是左眼

animator_eye_left.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
                android:duration="1000"
                android:interpolator="@android:anim/accelerate_interpolator"
                android:propertyName="pathData"
                android:valueFrom="M 28,40 Q 36,50 43,40"
                android:valueTo="M 28,40 Q 36,30 43,40"
                android:valueType="pathType"/>
```

##### 然后是右眼

animator_eye_right.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
                android:duration="1000"
                android:interpolator="@android:anim/accelerate_interpolator"
                android:propertyName="pathData"
                android:valueFrom="M 58,40 Q 66,50 73,40"
                android:valueTo="M 58,40 Q 66,30 73,40"
                android:valueType="pathType"/>
```

##### 最后是嘴部

animator_mouth_smile.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
                android:duration="1000"
                android:interpolator="@android:anim/accelerate_interpolator"
                android:propertyName="pathData"
                android:valueFrom="M 30,75 Q 50,55 70,75"
                android:valueTo="M 30,65 Q 50,85 70,65"
                android:valueType="pathType"/>
```

propertyName指定控制的属性
valueFrom和valueTo控制动画的起始值

- 注意：propertyName的指定属性如果为pathData，name还需要添加一个属性valueType="pathTpye"来告诉系统进行pathData变换

类似的情况：

- rotation进行旋转动画
- fillColor实现颜色动画 valueType="colorType"
- pathData进行形状、位置的变换 valueType="pathData"
- trimPathEnd与trimPathStart进行轨迹动画 valueType="floatType"

#### 3.在res/drawable/下创建animated-vector元素的矢量资源动画

animated_smile.xml

```java
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
                 android:drawable="@drawable/vector_smile">
    <target
        android:name="eye_left"
        android:animation="@animator/animator_eye_left"/>
    <target
        android:name="eye_right"
        android:animation="@animator/animator_eye_right"/>
    <target
        android:name="smile"
        android:animation="@animator/animator_mouth_smile"/>
</animated-vector>
```

- 这里需要注意的是target的name属性,必须与vector中需要作用的name属性保持一致，这样系统才能找到要实现动画的元素

#### 4.所有的XML文件都准备好了以后，就可以在代码中控制SVG动画了

先将AnimatedVectorDrawable XML 文件（animated_smile.xml）给一个ImageView设置背景

```java
      <ImageView
          android:id="@+id/image_smile"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:layout_centerInParent="true"
          android:src="@drawable/animated_smile"/>
```

在代码中 ((Animatable) imageView.getDrawable()).start();



### 总结

- 变化的曲线如果使用弧线来实现，是无法实现路径变化的过程，所以需要用二阶贝塞尔

- SVG动画可以实现简单的图形变换动画、轨迹动画，animated-vector目前还不能支持控制每个animator的执行时间和顺序



### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)

