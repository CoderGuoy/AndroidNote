## Tablayout介绍及使用详情

TabLayout provides a horizontal layout to display tabs.

Google官方对这个控件的大概意思是一个横向的布局标签
### 使用方法

首先，我们先来实现一个最简单的tablayout

#### Step1

```java
    <android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />
```
#### Step2

```java
  public class PlayTabLayout extends AppCompatActivity {
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tab_layout);
        TabLayout tabLayout = (TabLayout) findViewById(R.id.tablayout);
        tabLayout.addTab(tabLayout.newTab().setText("Tab 1"));
        tabLayout.addTab(tabLayout.newTab().setText("Tab 2"));
        tabLayout.addTab(tabLayout.newTab().setText("Tab 3"));
    }
  }
```

！[]image

这两步就可以实现一个简单的Tablayout滑动标签，so easy!

But,通常我们使用滑动标签逗搭配Viewpager来使用，看看如何实现

#### Step1
```java
    <android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        （fixed）默认固定的选项卡同时显示所有选项卡，（scrollable）可滚动选项卡并且可以包含较长的选项卡标签和更大数量
        app:tabMode="scrollable"  
        />

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"/>
```

#### Step2
```java
  public class PlayTabLayout extends AppCompatActivity {
    private List<String> mList = new ArrayList<String>();
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_tab_layout);
        for (int i = 1; i <= 8; i++) {
            mList.add("tab" + i);
        }
        TabLayout tabLayout = (TabLayout) findViewById(R.id.tablayout);
        ViewPager viewPager = (ViewPager) findViewById(R.id.viewpager);
        MyFragmentPagerAdapter adapter = new MyFragmentPagerAdapter(getSupportFragmentManager(), mList);
        viewPager.setAdapter(adapter);
        //将view pager与tablayout关联起来
        tabLayout.setupWithViewPager(viewPager);
    }
  }
```

```java
public class MyFragmentPagerAdapter extends FragmentPagerAdapter {
    private List<String> mList;
    public MyFragmentPagerAdapter(FragmentManager fm, List<String> list) {
        super(fm);
        this.mList = list;
    }
    @Override
    public Fragment getItem(int position) {//加载的Fragment
        return new PageFragment();
    }
    @Override
    public int getCount() {//标签页数
        return mList.size();
    }
    @Override
    public CharSequence getPageTitle(int position) {//标签标题
        return mList.get(position);
    }
}

```
PageFragment就是一个空白的Fragment

到这里最基础的搭配使用就ok了，下面贴上一些可以自己定制需求常用的方法

设置游标的颜色
setSelectedTabIndicatorColor(int color)

设置游标的高度 0就是没有
setSelectedTabIndicatorHeight(int height)

设置tablayout标签的模式 （fixed）默认固定的选项卡同时显示所有选项卡，（scrollable）可滚动选项卡并且可以包含较长的选项卡标签和更大数量
setTabMode()

设置tab标签文字颜色
setTabTextColors（）




