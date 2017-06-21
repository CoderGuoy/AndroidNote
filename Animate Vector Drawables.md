## Material Design动画 Animated Vector Drawables|矢量图片动画

> [AnimatedVectorDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedVectorDrawable.html)类可以去创建一个矢量资源的动画

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/animatedvectordrawable_smile.gif)

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

### 2.

####  1.

####  2.

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)

