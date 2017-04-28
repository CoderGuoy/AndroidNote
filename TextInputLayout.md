## Android Material Design 风格之 TextInputLayout | 文字输入布局 介绍及使用详情

> Layout which wraps an EditText (or descendant) to show a floating label when the hint is hidden due to the user inputting text. 
Also supports showing an error via setErrorEnabled(boolean) and setError(CharSequence)

> Google官方对这个控件的大概意思是 这是一个可以包裹EditText或者继承EditText的浮动标签，提示当由于用户输入text时而隐藏,
 还支持通过setErrorEnabled（boolean）和setError（CharSequence）显示错误
 
 一般来说， EditText 有一个 hint 属性，当 Edittext 中没有内容时，就会显示文字提示。一旦用户开始输入时，这个文字提示就会消失，取而代之地显示用户的输入。这样有一个坏处就是用户就无法了解到当前自己输入的是关于什么的信息。

而TextInputLayout解决了这个问题，用户开始输入时， hint 文字提示会变成 EditText 上方的标签，并伴随一个向上平移+缩放的优雅动画

说得再好不如来张图

![](https://github.com/CoderGuoy/Android-Material-Design/blob/master/screenshots/textinputlayout.gif)
 

### 使用方法
- TextInputLayout来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:24.2.0‘
    compile 'com.android.support:appcompat-v7:24.2.0'
}
```

####  首先，XML中添加布局文件

##### Step1

```java
 <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:padding="@dimen/px_90">

            <android.support.design.widget.TextInputLayout
                android:id="@+id/text_username"
                android:layout_width="match_parent"
                android:layout_height="wrap_content">

                <EditText
                    android:id="@+id/edittext_username"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"/>

            </android.support.design.widget.TextInputLayout>

            <android.support.design.widget.TextInputLayout
                android:id="@+id/text_password"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/px_90">

                <EditText
                    android:id="@+id/edittext_password"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"/>

            </android.support.design.widget.TextInputLayout>

            <Button
                android:id="@+id/btn_login"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/px_90"
                android:text="登录"
                android:textColor="@color/colorWrite"
                android:textSize="16sp"/>

        </LinearLayout>
```

#### 然后，编写相应的逻辑代码

##### Step2

```java

public class PlayTextInputLayout extends MvvmBaseActivity<ActivityPlayTextInputLayoutBinding> implements View.OnClickListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_play_text_input_layout);
        showContentView();
        bindingView.back.setOnClickListener(this);
        bindingView.btnLogin.setOnClickListener(this);
        bindingView.textUsername.setHint("用户名/Email/手机号");
        bindingView.textPassword.setHint("密码");
        bindingView.edittextUsername.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {

            }
            @Override
            public void afterTextChanged(Editable s) {
                username();
            }
        });
        bindingView.edittextPassword.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }
            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {

            }
            @Override
            public void afterTextChanged(Editable s) {
                password();
            }
        });
    }

    private boolean username() {
        if (TextUtils.isEmpty(bindingView.edittextUsername.getText())) {
            bindingView.textUsername.setError("用户名/Email/手机号不能为空");
            return false;
        } else {
            bindingView.textUsername.setError(null);
        }
        return true;
    }

    private boolean password() {
        if (TextUtils.isEmpty(bindingView.edittextPassword.getText())) {
            bindingView.textPassword.setError("密码不能为空");
            return false;
        } else {
            bindingView.textPassword.setError(null);
        }
        return true;
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.back:
                finish();
                break;
            case R.id.btn_login:
                if (username() && password()) {
                    finish();
                    ToastUtil.show("登录成功");
                }
                break;
        }
    }
}

```

- OK，这就完成了如效果图上的效果，这里并没有对UI进行优化，可以自己尝试一下，下面贴上一些可以自己定制需求常用的方法

```java

设置错误提示是否可用

setErrorEnabled(boolean)

设置错误提示

setError(CharSequence error) 

设置提示动画是否可用

setHintAnimationEnabled(boolean enabled) 

设置提示

setHint(CharSequence hint) 

设置提示text的颜色，大小，样式

setHintTextAppearance(int resId) 

设置提示text的字体

setTypeface(Typeface typeface) 

```

### 遇到的问题

#### Question1：TextInputLayout并没有实现当Edittext出现错误提示后输入内容完成之后隐藏错误提示

- 查看源码发现并没有对此进行处理，所以我们在使用的时候需要自己实现edittext的监听来实现完整的效果

### [完整代码点我下载](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
