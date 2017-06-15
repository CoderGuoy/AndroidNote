## Material Design动画 Curved motion | 曲线运动

> 在materialDesign中，动画是依赖于时间插值器和空间运动模式的。但是在Android5.0或者5.0以后的版本，用户可以自定义时间曲线和曲线运动模式

![]()

### 1.PathInterpolator

PathInterpolator类是一个新的插值器，它基于贝塞尔曲线或Path对象。这个插值器在1*1 的正方形上定义了曲线运动，以（0，0）和（1，1）点作为锚点，控制点则通过构造函数来指定，也可以使用xml文件的定义一个路径插值器

####  1.构造方法

- PathInterpolator(Path path)

- 创建任意Path

- PathInterpolator(float controlX, float controlY)

- 创建二阶贝塞尔曲线

- PathInterpolator(float controlX1, float controlY1, float controlX2, float controlY2)

- 创建三阶贝塞尔曲线

####  2.XML中

### 2.

####  1.

####  2.

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
