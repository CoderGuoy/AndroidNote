# Android XML绘图

## Shape

一般用shape定义的xml文件存放在drawable目录下，若项目没有该目录则新建一个，而不要将它放到drawable-hdpi等目录中

> 通过Shape可以在XML中绘制各种形状，可以定义下面四种类型的形状，通过android:shape属性指定：

```java
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    rectangle|矩形，默认的形状，可以画出直角矩形、圆角矩形、弧形等
    oval|椭圆形，用得比较多的是画正圆
    line|线形，可以画实线和虚线
    ring|环形，可以画环形进度条
    
    <!--设置圆角，只适用于rectangle类型，可分别设置四个角不同半径的圆角，当设置的圆角半径很大时，比如200dp，就可变成弧形边了-->
    <corners
        android:radius="integer"//四个角的角度(会被下面四个角度属性覆盖)  
        android:bottomLeftRadius="integer"//四个位置各自的角度
        android:bottomRightRadius="integer"     
        android:topLeftRadius="integer"
        android:topRightRadius="integer"/>
        
    <!--设置描边，可描成实线或虚线-->
    <stroke
        android:width="integer"//描边的颜色
        android:color="color"//描边的宽度
        android:dashGap="integer"//设置虚线时的横线之间的距离
        android:dashWidth="integer"/>//设置虚线时的横线长度
        
    <!--设置形状填充的颜色，只有android:color一个属性-->
    <solid android:color="@color/green"/>
    
    
    <!--设置内容与形状边界的内间距，可分别设置左右上下的距离-->
    <padding
        android:bottom="integer"//下内间距
        android:left="integer"//左内间距
        android:right="integer"//右内间距
        android:top="integer"/>//上内间距
    
    <!--设置形状的渐变颜色，可以是线性渐变、放射渐变、扫描性渐变-->   
    <gradient
        android:type 渐变的类型
              linear 线性渐变，默认的渐变类型
              radial 放射渐变，设置该项时，android:gradientRadius也必须设置
              sweep 扫描性渐变
        android:startColor 渐变开始的颜色
        android:endColor 渐变结束的颜色
        android:centerColor 渐变中间的颜色
        android:angle 渐变的角度，线性渐变时才有效，必须是45的倍数，0表示从左到右，90表示从下到上
        android:centerX 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
        android:centerY 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
        android:gradientRadius 渐变的半径，只有渐变类型为radial时才使用
        android:useLevel 如果为true，则可在LevelListDrawable中使用
        />
</shape>

```

Line 
画线时，有几点特性必须要知道的：
- 只能画水平线，画不了竖线
- 线的高度是通过stroke的android:width属性设置的
- size的android:height属性定义的是整个形状区域的高度
- size的height必须大于stroke的width，否则，线无法显示
- 线在整个形状区域中是居中显示的
- 线左右两边会留有空白间距，线越粗，空白越大
- 引用虚线的view需要添加属性android:layerType，值设为"software"，否则显示不了虚线

Ring
首先，shape根元素有些属性只适用于ring类型，先过目下这些属性吧：
- android:innerRadius 内环的半径
- android:innerRadiusRatio 浮点型，以环的宽度比率来表示内环的半径，默认为3，表示内环半径为环的宽度除以3，该值会被android:innerRadius覆盖
- android:thickness 环的厚度
- android:thicknessRatio 浮点型，以环的宽度比率来表示环的厚度，默认为9，表示环的厚度为环的宽度除以9，该值会被android:thickness覆盖
- android:useLevel 一般为false，否则可能环形无法显示，只有作为LevelListDrawable使用时才设为true




