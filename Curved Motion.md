## Material Design动画 Curved motion | 曲线运动

> 在materialDesign中，动画是依赖于时间插值器和空间运动模式的。但是在Android5.0或者5.0以后的版本，用户可以自定义时间曲线和曲线运动模式

![]()

### PathInterpolator

PathInterpolator类是一个新的插值器，它基于贝塞尔曲线或Path对象。这个插值器在1*1 的正方形上定义了曲线运动，以（0，0）和（1，1）点作为锚点，控制点则通过构造函数来指定，也可以使用xml文件的定义一个路径插值器

#### 构造方法

- PathInterpolator(Path path)

- 创建任意Path

- PathInterpolator(float controlX, float controlY)

- 创建二阶贝塞尔曲线

- PathInterpolator(float controlX1, float controlY1, float controlX2, float controlY2)

- 创建三阶贝塞尔曲线

#### XML中

自定义的PathInterpolator在Res/anim下创建

```java
  <pathInterpolator xmlns:android="http://schemas.android.com/apk/res/android"
    android:controlX1="0.4"
    android:controlY1="0"
    android:controlX2="1"
    android:controlY2="1"/>
```

xml文件中：controlX1，controlY1，controlX2，controlY2类似于贝塞尔曲线的控制点

#### 在API21(5.0)中提供了三种基本的曲线运动插值器：

- fast_out_linear_in | 快速加速并持续加速到结束
- fast_out_slow_in | 快速加速但缓慢减速到结束
- linear_out_slow_in | 甩向前缓慢到达最终值

#### 还有在API11中提供的16种插值器：

- accelerate_cubic | 加速度_立方体
- accelerate_decelerate | 加速度_减速
- accelerate_quad | 加速度_二次
- accelerate_quint | 加速度_五重峰
- anticipate | 开始向后,然后向前
- anticipate_overshoot | 开始向后，然后向前并超过目标值，最后返回最终值
- bounce | 到达目标值时有回弹效果并逐渐减小回弹力
- cycle | 重复动画一个周期
- decelerate_cubic | 减速度_立方
- decelerate_quad | 减速度_二次
- decelerate_quint | 减速度_五重峰
- linear | 速度恒定
- overshoot | 向前并超过目标值，最后返回最终值

### 使用方法

- animator.setInterpolator(new AnimationUtils().loadInterpolator(this, android.R.interpolator.fast_out_linear_in));
- animator.setInterpolator(new PathInterpolator(0.4f, 0f, 1f, 1f));

#### 代码实现

```java
        path = new Path();
        path.moveTo(view.getLeft(), view.getTop());
        path.lineTo(view.getLeft(), 600);
        animator = new ObjectAnimator().ofFloat(view, View.X, View.Y, path);
        animator.setInterpolator(new AnimationUtils().loadInterpolator(
                this, interpolator));
        animator.setDuration(2000);
        animator.start();
```

效果图代码比较多，想看全部代码点下面吧

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
