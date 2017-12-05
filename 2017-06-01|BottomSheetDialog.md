## Android 6.0新控件 BottomSheetDialog | 底部对话框 介绍及使用详情

extends AppCompatDialog 

> Base class for Dialogs styled as a bottom sheet

> 基于Dialog样式的一个底部对话框

先看看效果

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/bottomsheetdialog.gif)![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/bottomsheetdialog2.gif)

对于弹出的内容完全由自己来掌控,想实现什么样子就实现什么样子

### 使用方法
BottomSheetDialog来自design兼容包，使用需要添加依赖。android studio 添加依赖如下：
```xml
dependencies {
    compile ‘com.android.support:design:23.2.0+‘
}
```

####  1.XML中添加布局文件

```java
<LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:weightSum="2">

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1">

            <ImageView
                android:id="@+id/image_man"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="?android:selectableItemBackground"
                android:gravity="center"
                android:padding="10dp"
                android:src="@drawable/man"/>
        </RelativeLayout>

        <RelativeLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1">

            <ImageView
                android:id="@+id/image_women"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="?android:selectableItemBackground"
                android:gravity="center"
                android:padding="10dp"
                android:src="@drawable/women"/>
        </RelativeLayout>

    </LinearLayout>
```

####  2.在代码中使用

```java
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_play_bottom_sheet_dialog);
        showContentView();
        bindingView.bottomsheet.textviewTitle.setText("BottomSheetDialog");
        bindingView.bottomsheet.toolbarBack.setOnClickListener(this);
        bindingView.btnBsd1.setOnClickListener(this);
        initView();
    }

    private void initView() {
        View view = View.inflate(this, R.layout.bottom_dialog, null);
        ImageView man = (ImageView) view.findViewById(R.id.image_man);
        ImageView women = (ImageView) view.findViewById(R.id.image_women);
        man.setOnClickListener(this);
        women.setOnClickListener(this);

        bsd1 = new BottomSheetDialog(this);
        bsd1.setContentView(view);

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.toolbar_back:
                finish();
                break;
            case R.id.btn_bsd1:
                bsd1.show();
                break;
            case R.id.image_man:
                ToastUtil.show("男");
                bsd1.dismiss();
                break;
            case R.id.image_women:
                ToastUtil.show("女");
                bsd1.dismiss();
                break;
        }
    }
```
OK，这就完成了如效果图上的效果，可以自己尝试一下，下面贴上一些可以自己定制需求常用的方法

setCancelable(boolean cancelable) | 设置此对话框是否取消与BACK关联

setCanceledOnTouchOutside | 当设置窗口的边界之外触及这个对话框是否被取消


[官方API](https://developer.android.com/reference/android/support/design/widget/BottomSheetDialog.html)

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:coderguoy@gmail.com
