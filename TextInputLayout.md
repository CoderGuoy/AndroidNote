## Android Material Design风格的文字输入布局TextInputLayout介绍及使用详情

Layout which wraps an EditText (or descendant) to show a floating label when the hint is hidden due to the user inputting text. 
Also supports showing an error via setErrorEnabled(boolean) and setError(CharSequence)

Google官方对这个控件的大概意思是 这是一个可以包裹EditText或者继承EditText的浮动标签，提示当由于用户输入text时而隐藏,
 还支持通过setErrorEnabled（boolean）和setError（CharSequence）显示错误

### 使用方法
- TextInputLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:24.2.0‘
}
```

- 首先，XML中添加布局文件

#### Step1

```java
    <android.support.design.widget.TextInputLayout
        android:id="@+id/textinputlayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <EditText
            android:id="@+id/edittext"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

    </android.support.design.widget.TextInputLayout>
```
#### Step2

```java

```
- 效果图






- 到这里最基础的搭配使用就ok了，下面贴上一些可以自己定制需求常用的方法
```java


```

### 遇到的问题

- Question1：



## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
