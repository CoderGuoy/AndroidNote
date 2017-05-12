## Android 新控件 CardView 介绍及使用详情

[Material Design | 材料设计风格官网](https://material.io/)(英文不好的同学可以使用谷歌浏览器，打开网页后右键点翻译成中文)

一个具有圆角的背景和阴影的FrameLayout

CardView使用Android5.0上的 elevation 属性进行阴影，在Android5.0以下已阴影效果实现

由于圆角裁剪的昂贵性质，在Android5.0以下，CardView可以做到不夹住与圆角相交的孩子。相反，它增加了填充以避免这种情况
交点
在Lollipop之前，CardView在其内容中添加填充，并向该区域绘制阴影。这个填充量等于maxCardElevation +（1-cos45）* cornerRadius 
侧面和顶部和底部的 maxCardElevation * 1.5 +（1 - cos45）* cornerRadius 

由于填充用于偏移阴影内容，因此无法在CardView上设置填充
相反，您可以在XML或...中使用内容填充属性setContentPadding（int，int，int，int）在代码中设置边缘之间的填充CardView和CardView的孩子

请注意，如果您为CardView指定了精确的尺寸，因为它们的内容在棒棒糖和棒棒糖之后，平台之间的区域将不同。通过使用api版本
具体的资源值，可以避免这些变化。或者，如果您想要添加CardView在平台上的内部填充棒棒糖和之后，你可以设置
setUseCompatPadding（boolean）并传递 true 。

要以向下兼容的方式更改CardView的高程，请使用setCardElevation（float）。 CardView将在Lollipop和之前使用海拔API
棒棒糖，它会改变阴影的大小。为了避免在阴影尺寸变化时移动视图，阴影大小由getMaxCardElevation（）夹住。如果要更改高程
动态地，当CardView初始化时，您应该调用setMaxCardElevation（float）。


cardBackgroundColor
cardCornerRadius
cardElevation
cardMaxElevation
cardUseCompatPadding
cardPreventCornerOverlap

contentPadding
contentPaddingLeft
contentPaddingTop
contentPaddingRight
contentPaddingBottom
