## Material Design动画 View state changes|视图状态改变

> 可以根据View的可绘制状态定义一些可以在附件视图上运行的Animator,可以在具有<selector>元素的XML文件中定义.每个动画师都在嵌套的<item>元素中定义

![]()

### 1.定义一个XML资源selector
```java
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true">
        <set>
            <objectAnimator android:duration="@android:integer/config_shortAnimTime"
                            android:propertyName="translationZ"
                            android:valueTo="10"
                            android:valueType="floatType"/>
            <objectAnimator android:duration="@android:integer/config_shortAnimTime"
                            android:propertyName="rotationX"
                            android:valueTo="30"
                            android:valueType="floatType"/>
            <objectAnimator android:duration="@android:integer/config_shortAnimTime"
                            android:propertyName="rotationY"
                            android:valueTo="30"
                            android:valueType="floatType"/>
        </set>
    </item>
    <item android:state_pressed="false">
        <set>
            <objectAnimator android:duration="10000"
                            android:propertyName="translationZ"
                            android:valueTo="0"
                            android:valueType="floatType"/>
            <objectAnimator android:duration="@android:integer/config_shortAnimTime"
                            android:propertyName="rotationX"
                            android:valueTo="0"
                            android:valueType="floatType"/>
            <objectAnimator android:duration="@android:integer/config_shortAnimTime"
                            android:propertyName="rotationY"
                            android:valueTo="0"
                            android:valueType="floatType"/>
        </set>
    </item>
</selector>
```

### 2.使用方法

- 布局：android:stateListAnimator属性将其分配给你的视图

- 代码：使用AnimationInflater.loadStateListAnimator()方法，并且通过View.setStateListAnimator()方法分配动画到你的视图上


### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
