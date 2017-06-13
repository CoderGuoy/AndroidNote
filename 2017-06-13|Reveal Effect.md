## Material Design动画 Reveal Effect|揭示效果

![](https://github.com/CoderGuoy/AndroidNote/blob/master/screenshots/RevealEffect.gif)

### 核心方法
```java
  ViewAnimationUtils.createCircularReveal(
    View view,//视图将被剪辑到动画圈子
    int centerX,//开始动画的起点x坐标(相对于目标view而言)
    int centerY,//开始动画的起点y坐标(相对于目标view而言)
    float startRadius,//动画圆的起始半径
    float endRadius//动画圆的半径结束
  );
```

### 效果图代码实现

####  1.布局
```java
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <Button
            android:id="@+id/btn_revealeffect"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="展开/收缩"/>

        <ImageView
            android:id="@+id/view_revealeffect"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:src="@drawable/gank"
            android:visibility="invisible"/>

    </LinearLayout>
```

####  2.逻辑代码
```java
public class PlayRevealEffect extends MvvmBaseActivity<ActivityPlayRevealEffectBinding> implements View.OnClickListener {

    private boolean isFirst = true;//是否是第一次点击
    private Animator animator;
    private int width;// 屏幕宽度（像素）
    private int height;// 屏幕高度（像素）

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_play_reveal_effect);
        GetPiexels();
        bindingView.btnRevealeffect.setOnClickListener(this);
    }

    private void GetPiexels() {
        DisplayMetrics metric = new DisplayMetrics();
        getWindowManager().getDefaultDisplay().getMetrics(metric);       
        width = metric.widthPixels;       
        height = metric.heightPixels;
    }

    @Override
    public void onClick(View v) {
                if (isFirst) {
                    animator = ViewAnimationUtils.createCircularReveal(
                            bindingView.viewRevealeffect,
                            width / 2,
                            height / 2,
                            0,
                            height
                    );
                    isFirst = false;
                } else {
                    animator = ViewAnimationUtils.createCircularReveal(
                            bindingView.viewRevealeffect,
                            0,
                            0,
                            height,
                            0
                    );
                    isFirst = true;
                }
                animator.addListener(new Animator.AnimatorListener() {
                    @Override
                    public void onAnimationStart(Animator animation) {
                        if (!isFirst) {
                            bindingView.viewRevealeffect.setVisibility(View.VISIBLE);
                        }
                    }

                    @Override
                    public void onAnimationEnd(Animator animation) {
                        if (isFirst) {
                            bindingView.viewRevealeffect.setVisibility(View.INVISIBLE);
                        }
                    }

                    @Override
                    public void onAnimationCancel(Animator animation) {
                    }

                    @Override
                    public void onAnimationRepeat(Animator animation) {
                    }
                });
                animator.setDuration(500);
                animator.start();
    }
}

```

### 完整代码点我下载[GitHub](https://github.com/CoderGuoy/Coder)

## Thank you

- 以上仅本人学习中遇到的问题，如有更多意见欢迎随时交流 [issues](https://github.com/CoderGuoy/MetalDesign/issues/1)
- email:andriodguoy@gmail.com(安卓的单词不是我打错了，是不允许使用，故i和o位置调换了)
